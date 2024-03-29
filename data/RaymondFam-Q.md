## Un-indexed Parameters in Events
Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute indexed which will cause the respective arguments to be treated as log topics instead of data. The following links are some of the instances entailed:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L49-L56

## Unused State Variable and Unnecessary Code Line
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L13

The state variable `decimals` has been assigned `priceFeed1.decimals()` in the constructor, but it is never used in the contract. It could have been used in the following line of code such that:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L73

```
        int256 decimals10 = int256(10**(18 - decimals);
```
That said, line 73 could be removed, and have line 74 rewritten as follows: 
 
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L74

```
        nowPrice = nowPrice * 1e10;
```
This is because almost all Arbitrum data feeds entail 8 decimals, making line 73 a redundant step. 

Additionally, the code logic here presumes that priceFeed1 and priceFeed2 will only be dealing with data feeds of 8 decimals. Otherwise, the following line of code could readily truncate to zero if `priceFeed1.decimals()` is equal to a different value, e.g. 18.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L78

## safeTransfer and safeTransferFrom
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L167
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L228
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L231

`SafeTransferLib` has been inherited from `SemiFungibleVault.sol`. Where possible, use `safeTransfer` and `safeTransferFrom` instead of `transfer` and `transferFrom` to cater for non-standard ERC20 tokens that don’t have boolean return values, just in case.

Note: The following line of code will have to stay intact, serving to conform with `IWETH.sol` though:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190

## Zero Address and Zero Value Checks for StakingRewards.sol

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L81-L86

Zero address and zero value checks should be implemented when deploying the contract. This is because there is no setter functions for these state variables. In the event a mistake was done at `RewardsFactory.sol`, not only that all calls associated with it would be non-functional, the contract would also have to be redeployed.

## Vaults Usable by DAOs
Since all vaults are going to be usable by DAOs, i.e., accepting deposits and withdraws on behalf of other users, by using approve ERC1155 functions on withdraw, and recipient/owner params inside both deposit/withdraw functions, the following instance should have `&&` replaced by `||` in the if condition: 

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L215-L218