## `getNextEpoch` could become unusable 
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L438-L452
In the long run, this function could become unusable, as it would consume too much gas. This could be fixed with binary search algorithm

## `epochHasEnded` modifier not working as intended 
If someone would call `withdraw` after epoch expires but before `triggerEndEpoch` call, modifier `epochHasEnded` would not work as intended and would not revert. Withdraw would revert on `beforeWithdraw` when it would try to divide by idFinalTVL which is 0. Since it used only in `withdraw`, there is no risk.
Solution: add idFinalTVL check in `epochHasEnded `modifier
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L96

## Redundant token transfer
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L168
this line can be removed, right after it tokens transfered other way 

## eip-4626-like functionality is broken
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L244-L252
Here, totalAssets always return number of shares, instead of underlying asset. This renders this functions 
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L143-L228
useless, as shares would always be equal to assets.
eip-4626 deposit and withdraw functions are not used either, since they get overriden in Vault.sol contract.
Solution: remove ERC4626 vault functionality alltogether to greatly simplify codebase.