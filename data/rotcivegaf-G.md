# Gas report

## [G-01] Use `immutable`

List of variables:
  - Controller.sol#L13: `sequencerUptimeFeed`
  - PegOracle.sol#L10: `oracle1`
  - PegOracle.sol#L11: `oracle2`
  - PegOracle.sol#L13: `decimals`
  - PegOracle.sol#L15: `priceFeed1`
  - PegOracle.sol#L16: `priceFeed2`
  - RewardsFactory.sol#L09: `admin`
  - RewardsFactory.sol#L10: `govToken`
  - RewardsFactory.sol#L11: `factory`

## [G-02] Remove `assert` of [Vault.sol#L190](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L190)