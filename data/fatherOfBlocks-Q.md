**Controller.sol**
- L4 - ERC20 is imported but it is never used.

**SemiFungibleVault.sol **
- L85/87/94 - The concept of asset and assets can be confusing, it would be better to use other more declarative names.

**Vault.sol**
- L95/96 - The concept of id and block.timestamp are not ideas that can be compared, probably the name id of the input should be something else.

- L393-398 - There is commented code that is not used and does not explain anything about the implementation, this should be deleted.

**VaultFactory.sol**
- L34/97 - An AddressNotController() error is created that is never used, it also happens with the changedVaultFee() event.

**rewards/RewardsFactory.sol**
- L25 - There is commented code that is not used and does not explain anything about the implementation, this should be deleted.