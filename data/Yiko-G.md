https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L187
In Vault.sol, at line 187
Instead of "msg.value > 0" using "msg.value != 0" might save gas; as msg.value can never be smaller than 0.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L443
In Vault.sol, at line 443
Instead of using "i++", using "++i" might save gas.