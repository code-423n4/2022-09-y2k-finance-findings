# LOW

## [L-01] `transfer`/`transferFrom` doesn't handle ERC20 tokens with no return value

Some tokens (like USDT) don't correctly implement the EIP20 standard and their `transfer`/`transferFrom` functions return void instead of a success boolean. Calling these functions with the correct EIP20 function signatures will always revert.

It is recommended to use OpenZeppelin’s SafeERC20 versions with the safeTransfer and safeTransferFrom functions that handle both cases, with return value or not.

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```solidity
122:        ) SemiFungibleVault(ERC20(_assetAddress), _name, _symbol) {
167:        asset.transferFrom(msg.sender, address(this), shares);
228:        asset.transfer(treasury, feeValue);
231:        asset.transfer(receiver, entitledShares);
365:        asset.transfer(_counterparty, idFinalTVL[id]);
```
