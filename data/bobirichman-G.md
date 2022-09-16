# GAS REPORT

## [GAS] Use assembly opcodes iszero in the following locations


### Proof of concept:
- [SemiFungibleVault.sol#L196](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L196)
- [StakingRewards.sol#L159](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L159)

## [GAS] Mark as payable If has onlyOwner modifier
In order to save gas you can put a payable modifier for functions that are called by protocol owners.

### Proof of concept:
- [RewardsDistributionRecipient.sol#L23](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsDistributionRecipient.sol#L23)
- [Vault.sol#L310](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L310)

## [GAS] Use abiEncodePacked()


### Proof of concept:
- [RewardsFactory.sol#L117](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L117)
- [RewardsFactory.sol#L149](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L149)

## [GAS] Use > instead != to compare uint with 0


Example: [StakingRewards.sol#L118](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L118)

--