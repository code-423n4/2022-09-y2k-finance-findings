# GAS REPORT

## [GAS 00] Unnecessary caching of msg.sender


### Proof of concept:
- [StakingRewards.sol#L159](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L159)
- [StakingRewards.sol#L170](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L170)
- [Vault.sol#L444](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L444)
- [Vault.sol#L391](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L391)
- [StakingRewards.sol#L84](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L84)

--