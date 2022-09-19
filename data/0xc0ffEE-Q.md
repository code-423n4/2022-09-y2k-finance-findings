1. Return values ignored below:
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L167
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L228
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L231
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L365
Should check return values or use OpenZeppelin's SafeERC20 wrapper functions

2. The `Vault.depositETH()` function has a bad UX. It will always fail if `msg.sender` has not approved WETH allowance to the `Vault`. So the number of transaction needed to depositETH is still 2 transactions. Also, with this flow, it still costs more gas than just call to `WETH.approve()` and then call `Vault.deposit()`
Consider change the logic of this function for better UX