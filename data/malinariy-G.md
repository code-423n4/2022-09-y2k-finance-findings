# Gas Optimization Report

1. State variables only set in the constructor should be declared immutable

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L9-L11

2. Public functions not called by the contract should be declared external instead
Calls to external functions are cheaper than public functions. Thus, if a function is not used internally in any contract, it should be set to external to save gas and improve code readability.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L145-L148
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L178-L186
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L248-L253
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L295
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L366
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L385-L388
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L148-L150
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L198
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L438-L441
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L221-L225
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L237
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L244
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L251-L255
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L263-L267

3. Use `abi.encodePacked()` not `abi.encode()`
`abi.encode` pads extra null bytes at the end of the call data which is normally unnecessary. In general, `abi.encodePacked` is more gas-efficient.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L118
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L150

4. Comparison operators
In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `> and =`. 
`Replace <= with <, and >= with >.` Do not forget to increment/decrement the compared variable. However, when 1 is negligible compared to the variable.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L302

5. It costs more gas to initialize variables with their default value than letting the default value be applied

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L443

6. `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L443

7. `require()/revert()` strings longer than 32 bytes cost extra gas

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L118