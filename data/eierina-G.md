# Gas Optimizations

## Prefer fixed size arrays in place of dynamic sized arrays where fits

dynamic arrays are more expensive than fixed arrays in terms of gas, prefer fixed array size when it fits the case.

### VaultFactory.sol can use fixed size array for market vaults
Vaults always are in a pair, and are mapped to a dynamic array. Changing to a fixed array can save a lot of gas (e.g. ~22100 gas saved for an insert).

Before: [PASS] testMarketDoesNotExist() (gas: 6066351)</br>
After: [PASS] testMarketDoesNotExist() (gas: 6044191)

File-Line: [VaultFactory.sol:L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L119)
```solidity
-    mapping(uint256 => address[]) public indexVaults; //[0] hedge and [1] risk vault
+    mapping(uint256 => address[2]) public indexVaults; //[0] hedge and [1] risk vault
```

File-Line: [VaultFactory.sol:L313](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L313), [VaultFactory.sol:L331](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L331), [VaultFactory.sol:L352](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L352)
```solidity
-        address[] memory vaults = indexVaults[_marketIndex];
+        address[2] memory vaults = indexVaults[_marketIndex];
```

File-Line: [VaultFactory.sol:L388](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L388)

```solidity
-        returns (address[] memory vaults)
+        returns (address[2] memory vaults)
```

### RewardsFactory.sol can use fixed size array for staking rewards

File-Line: [RewardsFactory.sol:L27](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L27)
```solidity
-    mapping(bytes32 => address[]) public hashedIndex_StakingRewards; //Hashed Index, Staking Rewards [0] = insrance, [1] = risk
+    mapping(bytes32 => address[2]) public hashedIndex_StakingRewards; //Hashed Index, Staking Rewards [0] = insrance, [1] = risk

```

## Avoid unnecessary calls to external contracts

Making external calls to other contracts costs gas due to the call itself and the code executed by the external call. Unnecessarily repeating external calls duplicate costs. Prefer caching external call response where possible.

### modifier isDisaster is large, forces duplicate external calls and is used once only

Part of the code used in [isDisaster](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L83) modifier is repeated in the only function that uses the modifier [triggerDepeg](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L148), and the duplicated code includes an external call.
Suggestion is to:
- remove the modifier, and do the validation inline same as it is done;
- moreover cache getLatestPrice calls as they are repeated within the function.
 
 This change not only reduces the total lines of code but also reduces gas costs. 

```solidity
-    function triggerDepeg(uint256 marketIndex, uint256 epochEnd)
-        public
-        isDisaster(marketIndex, epochEnd)
+    function triggerDepeg(uint256 marketIndex, uint256 epochEnd) public
     {
-        address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
+        address[2] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
         Vault insrVault = Vault(vaultsAddress[0]);
         Vault riskVault = Vault(vaultsAddress[1]);

+        if(insrVault.idExists(epochEnd) == false)
+            revert EpochNotExist();
+
+        int256 latestPrice = getLatestPrice(insrVault.tokenInsured()); // @audit cache getLatestPrice call
+        if(insrVault.strikePrice() < latestPrice) // @audit use cached price
+            revert PriceNotAtStrikePrice(latestPrice); // @audit use cached price
+
+        if(insrVault.idEpochBegin(epochEnd) > block.timestamp)
+            revert EpochNotStarted();
+
+        if(block.timestamp > epochEnd)
+            revert EpochExpired();
+
         //require this function cannot be called twice in the same epoch for the same vault
         if(insrVault.idFinalTVL(epochEnd) != 0)
             revert NotZeroTVL();
         if(riskVault.idFinalTVL(epochEnd) != 0)
             revert NotZeroTVL();

         insrVault.endEpoch(epochEnd, true);
         riskVault.endEpoch(epochEnd, true);

         insrVault.setClaimTVL(epochEnd, riskVault.idFinalTVL(epochEnd));
         riskVault.setClaimTVL(epochEnd, insrVault.idFinalTVL(epochEnd));

         insrVault.sendTokens(epochEnd, address(riskVault));
         riskVault.sendTokens(epochEnd, address(insrVault));

         VaultTVL memory tvl = VaultTVL(
             riskVault.idClaimTVL(epochEnd),
             insrVault.idClaimTVL(epochEnd),
             riskVault.idFinalTVL(epochEnd),
             insrVault.idFinalTVL(epochEnd)
         );

         emit DepegInsurance(
             keccak256(
                 abi.encodePacked(
                     marketIndex,
                     insrVault.idEpochBegin(epochEnd),
                     epochEnd
                 )
             ),
             tvl,
             true,
             epochEnd,
             block.timestamp,
-            getLatestPrice(insrVault.tokenInsured()) // @audit remove duplicate call
+            latestPrice // @audit use cached price
         );
     }             
```

### xyz.sol 123



### modifier `isDisaster` forces duplicate calls to VaultFactory::getVaults()

File-Line: [Controller.sol:L148-L152](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L148-L152)

Modifier isDisaster is used only by `triggerDepeg` and never reused. Move isDisaster verification code inline to `triggerDepeg` and remove duplicate calls to VaultFactory::getVaults().

### getLatestPrice called multiple times within a function

File-Line: [.sol:L-L]()

## Use unchecked where no risk for overflow/underflow

File-Line: [VaultFactory.sol:L195](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L195)

```solidity
+    unchecked {
         marketIndex += 1;
+    }
```

File-Line: [Vault.sol:L88-L89](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L88-L89)

```solidity
    modifier epochHasNotStarted(uint256 id) {
+    unchecked {        
        if(block.timestamp > idEpochBegin[id] - timewindow)
            revert EpochAlreadyStarted();
+    }            
        _;
    }
```
> **_NOTE:_** requires timewindow to have bounds checked as described [here](#-nc-04--timewindow-has-no-bounds). 

File-Line: [Vault.sol:L227](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L227)

```solidity
+   unchecked {
        entitledShares = entitledShares - feeValue; // @audit cannot underflow, feeValue is a fraction of entitledShares
+   }
```

File-Line: [Vault.sol:L266](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L266)

```solidity
+   unchecked {
        return (amount * epochFee[_epoch]) / 1000; // @audit cannot realistically overflow, epochFee is limited to a max of 150
+   }
```

File-Line: [Vault.sol:L438](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L438)

Cheaper getNextEpoch below, multiple optimizations.

```solidity
function getNextEpoch(uint256 _epoch)
    public
    view
    returns (uint256 nextEpochEnd)
{
+   unchecked {
+       uint256 scanLength = epochs.length > 0 ? epochs.length - 1 : 0;
+       for(uint256 i = 0; i < scanLength; ++i) {
+           if(epochs[i] == _epoch) {
+               nextEpochEnd = epochs[i+1];
+               break;
+           }
+       }
+   }
-   for (uint256 i = 0; i < epochsLength(); i++) {
-       if (epochs[i] == _epoch) {
-           if (i == epochsLength() - 1) {
-               return 0;
-           }
-           return epochs[i + 1];
-       }
-   }
}
```


## Prefer pre-increment, post-increment operators

Prefer increment operator rather than adding or subtracting 1. Pre-increment if it fits the case, post-increment otherwise.

File-Line: [VaultFactory.sol:L195](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L195)

```solidity
+    unchecked {
-        marketIndex += 1;
+        ++marketIndex;
+    }
```