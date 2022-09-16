# GAS REPORT

## [GAS] Caching msg.sender is unnecessary


### Proof of concept:
- [Controller.sol#L233](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L233)
- [PegOracle.sol#L105](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L105)

## [GAS] Consider using custom errors
You can utilize customized errors in require statements to save gas.

### Proof of concept:
- [PegOracle.sol#L125](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L125)
- [StakingRewards.sol#L225](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L225)
