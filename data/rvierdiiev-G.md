1.	Cache `getLatestPrice(vault.tokenInsured())` into variable as you use it int 2 places.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L96-L99
2.	Use `external` modifier instead of `public` if you don’t call method from a contract to save deployment gas.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L148
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L217
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L277
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L287
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L295
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L307
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L336
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L350
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L360
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L439
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L186
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L180
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L253
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L295
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L308
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L327
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L345
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L366
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L385
3.	Use `if (answer > 0)` to save gas. Do not create Boolean variable.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L277-L280
4.	Use `>` instead of `!=0` to save gas.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L157
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L159
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L215
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L217
5.	Use ++i instead of i++ for loops. Also cache `epochsLength()` into variable.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443
