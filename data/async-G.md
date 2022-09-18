# Gas Savings

## Short circut expensive operations
In `Controller.isDisaster()` the `if` tree can be reordered to fail early before expensive external calls are made.

```javascript
    modifier isDisaster(uint256 marketIndex, uint256 epochEnd) {
        address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
        if(
            block.timestamp > epochEnd // no external call
            )
            revert EpochExpired();
        if(
            vaultsAddress.length != VAULTS_LENGTH
            )
            revert MarketDoesNotExist(marketIndex);

        address vaultAddress = vaultsAddress[0]; 
        Vault vault = Vault(vaultAddress);

        if(vault.idExists(epochEnd) == false)
            revert EpochNotExist();

        if(
            vault.strikePrice() < getLatestPrice(vault.tokenInsured()) // save this return value for use in the revert
            )
            revert PriceNotAtStrikePrice(getLatestPrice(vault.tokenInsured())); 

        if(
            vault.idEpochBegin(epochEnd) > block.timestamp)
            revert EpochNotStarted();
        _;
    }
```


## Multiple external calls can be avoided with saved variable

In `Controller.isDisaster()` there are two external calls to `vault.tokenInsured()`. Save gas by storing the return value of `getLatestPrice(vault.tokenInsured())` which can be used in the revert.

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L96-L99

```javascript
if(
vault.strikePrice() < getLatestPrice(vault.tokenInsured())
)
revert PriceNotAtStrikePrice(getLatestPrice(vault.tokenInsured())); 
```


## Use `external` instead of `public` where possible
Functions with the `public` visibility modifier are costlier than `external`. Default to using the `external` modifier until you need to expose it to other functions within your contract.

Consider changing the visibility of these functions in to `external`

In Vault
- endEpoch
- setClaimTVL
- sendTokens
- getNextEpoch

In PegOracle
- getOracle1_Price


## Cache storage values in memory for loop
In `Vault.getNextEpoch()` the return of `epochsLength()` is not cached. 

Anytime you are reading from storage more than once, it is cheaper to cache variables in memory. An SLOAD cost 100 GAS while MLOAD and MSTORE only cost 3 GAS.

This is especially true in for loops when using the length of a storage array as the condition being checked after each loop. Caching the array length in memory can yield significant gas savings when the array length is high.

```solidity
// Before
for (uint256 i = 0; i < epochsLength(); i++) { //@audit cache this length
    if (epochs[i] == _epoch) {
        if (i == epochsLength() - 1) {
            return 0;
        }
        return epochs[i + 1];
}

// After
uint memory length = epochsLength() - 1;
for (uint256 i = 0; i < length; i++) { // length cached
    if (epochs[i] == _epoch) {
        if (i == length) {
            return 0;
        }
        return epochs[i + 1];
}
```


## Struct should be efficently packed

Reading and writing from each storage slot cost gas. Packing variables lets us reduce the number of slots our contract needs.

To allow the EVM to optimize your storage layout, make sure to order your storage variables and struct members such that they can be packed tightly. For example, in VaultFactory.sol if we reduce the size of some uint types the Struct `MarketVault` can be packed like this:

```solidity
// Before - 6 slots
struct MarketVault{ 
	uint256 index;
    uint256 epochBegin;
    uint256 epochEnd;
    Vault hedge;
    Vault risk;
    uint256 withdrawalFee;
}

// After - 3 slots; the first 4 values fit in a single 32 bytes slot
struct MarketVault{ 
	uint16 index; // 2 bytes
    uint32 epochBegin; // 4 bytes; max time good until 2106
    uint32 epochEnd; // 4 bytes
    Vault hedge; // address 20 bytes
    Vault risk;
    uint256 withdrawalFee;
}
```


## Use custom errors
Consider replacing revert strings with custom errors. [Custom errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met.)

This can be implemented in contracts SemiFungibleVault, Vault and PegOracle.