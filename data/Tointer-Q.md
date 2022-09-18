## `getNextEpoch` could become unusable 
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L438-L452
In the long run, this function could become unusable, as it would consume too much gas. This could be fixed with binary search algorithm

## `epochHasEnded` modifier not working as intended 
If someone would call `withdraw` after epoch expires but before `triggerEndEpoch` call, modifier `epochHasEnded` would not work as intended and would not revert. Withdraw would revert on `beforeWithdraw` when it would try to divide by idFinalTVL which is 0. Since it used only in `withdraw`, there is no risk.
Solution: add idFinalTVL check in `epochHasEnded `modifier
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L96

## Can't work with tokens with more then 18 decimals
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L299
This will revert if priceFeed outputs price with more then 18 decimals. Those are pretty rare, but it should be noted