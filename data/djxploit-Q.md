## Loop on unbounded arrays can lead to DOS attacks when the size gets too big
In loop https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443-L450, `epochs` is an unbounded array, which determines the length of the loop. So that may lead to DOS

## Same argument is passed twice while emitting
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L171 : `shares` is passed twice. This may break offsite calculations that depends on the emit values

## Use revert instead of assert
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190

## Missing emits for events
In https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L97, `changedVaultFee` event is missing emit which prevents the intended data from being observed easily by off-chain interfaces.  Recommend adding emits or remove event declarations

## Unused error functions
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L34

## Use of block attributes like block.timestamp are risky, as they can be manipulated by miners
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L102
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L106
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L189
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L203

## Immutable address should be 0-checked. Missing 0-address check, can lead to unintended issues, which may cause re-deployment of the contract
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L70

## Custom error messages should be used instead of revert strings, to save deployment cost
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L91
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L116-L118
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L165
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187

## All setter functions should emit
`changeTreasury`, `changeTimewindow` and `changeController` are critical functions and should emit or be timelocked
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L277
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L287
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L295

## Unused modifier
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L73 : `onlyAdmin` modifier is not used

## Multiple named return values
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L152-L174
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L203-L234
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L378-L426

