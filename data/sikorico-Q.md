# QA REPORT

## [QA 00] Not verified input for important functions


### Proof of concept:
- [RewardsDistributionRecipient.sol#L23](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsDistributionRecipient.sol#L23)
- [Vault.sol#L295](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L295)
- [Controller.sol#L125](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L125)
- [VaultFactory.sol#L295](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L295)
- [Owned.sol#L22](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/Owned.sol#L22)

## [QA 01] Missing MIT licence for the following contracts


### Proof of concept:
- [Owned.sol](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/Owned.sol)
- [RewardsDistributionRecipient.sol](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsDistributionRecipient.sol)

## [QA 02] Remove the name from the following unused function parameters


### Proof of concept:
- [SemiFungibleVault.sol#L286](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L286)
- [StakingRewards.sol#L80](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L80)
- [Vault.sol#L122](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L122)

## [QA 03] Both return statement and named return
For readability purposes consider having one of the two return options (for the following functions)

### Proof of concept:
- [Vault.sol#L442](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L442)
- [Controller.sol#L265](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L265)
- [Vault.sol#L214](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L214)
- [VaultFactory.sol#L186](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L186)
- [Vault.sol#L163](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L163)
