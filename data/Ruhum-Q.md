# Report

- [Low](#low)
  - [L-01: PegOracle not usable on mainnet](#l-01-pegoracle-not-usable-on-mainnet)
- [Non-Critical](#non-critical)
  - [N-01: emit an event when changing contract's configuration](#n-01-emit-an-event-when-changing-contracts-configuration)

# Low

## L-01: PegOracle not usable on mainnet

The PegOracle contract expects to use two different oracles to compute the peg between two assets:
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L46-L83

In the contest's README, you provide stETH and ETH as an example. Because Chainlink only has an oracle for stETH/USD and ETH/USD, you compute the ratio between stETH/ETH yourself. But, this oracle combination is not present on every network. On mainnet, there's only one for [stETH/ETH](https://etherscan.io/address/0x86392dC19c0b719886221c78AB11eb8Cf5c52812). The PegOracle contract won't work on mainnet.

The readme said that deployment is only planned for Arbitrum so this isn't an issue now. But it might be in the future if you decide to expand to other networks.

# Non-Critical

## N-01: emit an event when changing contract's configuration

It allows third parties to track those changes easier.

- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L277
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L287
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L295