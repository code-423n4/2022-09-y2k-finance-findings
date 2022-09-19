# QA Report

## Table of Contents

### Summary

### Low

- [Lack of validation in `createAssets`](#lack-of-validation-in-createassets)
- [use of `assert()`](#use-of-assert)
- [Unbounded loop can result in DoS](#unbounded-loop-can-result-in-dos)


# Summary

> The main concerns are with the lack of validation in `createAssets`, which can potentially result in the creation of obsolete markets.

# Lack of validation in `createAssets`

`Vault.createAssets` does not have enough checks for `epochBegin`, which means it is technically possible for the admin to create an obsolete market.

## Impact

Low


## Proof Of Concept

`Vault.createAssets` only checks that `epochBegin >= epochEnd`.

```solidity
317: if(epochBegin >= epochEnd)
318:             revert EpochEndMustBeAfterBegin();
319: 
320:         idExists[epochEnd] = true;
321:         idEpochBegin[epochEnd] = epochBegin;
```

But for a user to be able to call `deposit`, the modifier `epochHasNotStarted` must not revert, ie `idEpochBegin[id] - timewindow` must be greater than the current `block.timestamp`

```solidity
87:     modifier epochHasNotStarted(uint256 id) {
88:         if(block.timestamp > idEpochBegin[id] - timewindow)
89:             revert EpochAlreadyStarted();
90:         _;
91:     }
92: 
```

This means that the following case can happen:

- Admin calls `VaultFactory.createNewMarket`, with `epochBegin == N` and `epochEnd == N + 100`.
- The current block.timestamp is `N - 43,200` (ie N - 0.5 days)
- This means `idEpochBegin[id] - timewindow == N - 1 days < block.timestamp`: the modifier `epochHasNotStarted()` will always revert for this market. Users cannot call `deposit`, the market needs to be re-deployed.

## Tools Used

Manual Analysis



## Mitigation

Add stronger validation in `Vault.createAssets`: 

```diff
-317: if(epochBegin >= epochEnd)
+317: if((epochBegin >= epochEnd) || (epochBegin - timewindow <= block.timestamp))
318:             revert EpochEndMustBeAfterBegin();
319: 
320:         idExists[epochEnd] = true;
321:         idEpochBegin[epochEnd] = epochBegin;
```

You can also consider replacing `timewindow` with a greater number.

# use of `assert()`

As per Solidity [documentation](https://docs.soliditylang.org/en/v0.6.12/control-structures.html?highlight=assert#id4), `assert` should be avoided, except to check invariants.

It is used here in `depositETH`, asserting the `WETH.transfer` call. 

## Impact

Low


## Tools Used

Manual Analysis



## Mitigation

```diff
+190: require(IWETH(address(asset)).transfer(msg.sender, msg.value));
-190: assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
```




# Unbounded loop can result in DoS

## PROBLEM

When `epochs.length` increases, it results in the gas cost of `Vault.getNextEpoch` getting higher.
Should `epochs` reach a certain size, the gas cost of the function call might get too high, meaning every call to `Vault.getNextEpoch` would revert.

## Impact

Low

## TOOLS USED

Manual Analysis

### PROOF-OF-CONCEPT

`epochs` gets larger everytime `createAssets` is called upon the creation of a new market:

```solidity
322: epochs.push(epochEnd)
```

This result in the call to `Vault.getNextEpoch` getting more gas consuming.

```solidity
443: for (uint256 i = 0; i < epochsLength(); i++) {
444:             if (epochs[i] == _epoch) {
445:                 if (i == epochsLength() - 1) {
446:                     return 0;
447:                 }
448:                 return epochs[i + 1];
449:             }
450:         }
```

## MITIGATION

Either keep `epochs` to a certain size with an upper boundary, or consider using an [Enumerable set](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/EnumerableSet.sol).
