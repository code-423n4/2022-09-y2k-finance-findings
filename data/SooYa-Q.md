## Wrong Comment 
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L265

``` // 0.5% = multiply by 1000 then divide by 5 ```

It should be:
``` // multiply by 5 then divide by 1000```


## `require()` Should Be Used Instead Of `assert()`
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert() do. assert() should be avoided even past solidity version 0.8.0 as its documentation states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

There is 1 instance for this issue:
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190