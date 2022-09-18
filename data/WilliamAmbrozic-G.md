## Gas Optimization Report
17/09/2022 14:59
[Y2K Finance](https://github.com/code-423n4/2022-09-y2k-finance)
**Note:** Contracts are ordered as they appear in the [repo](https://github.com/code-423n4/2022-09-y2k-finance). Suggestions are ordered by the line they occurred.
**Tools Used:** Remix, Manual Analysis
***
### PegOracle.sol

**Require statements under 32 characters save on gas**
- line(s) in question: [23](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol?plain=1#L23), [24](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol?plain=1#L24)
***
### StakingRewards.sol
**Making `public` variables `immutable` saves on gas**

`id` is only defined in the constructor; therefore can be changed to `immutable`.
- line(s): [41](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol?plain=1#L41)

**`uint != 0` saves gas relative to `uint > 0`**
- line(s) in question: [134](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol?plain=1#L134)

*Suggestion:* Change `if (reward > 0)` to `if (reward != 0)`.

**Require statements under 32 characters save on gas**
- line(s) in question: [219](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol?plain=1#L219), [228](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol?plain=1#L228)
***
### SemiFungibleVault.sol
**Require statements under 32 characters save on gas**
- line(s) in question: [118](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol?plain=1#L118)
***
### Vault.sol
**Modifiers only used once**
- **epochHasNotStarted:** line [87](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol?plain=1#L87)
- **epochHasNotStarted:** line [95](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol?plain=1#L95)

*Suggestion*: in-line the modifiers per function.
***
### VaultFactory.sol

**`i += 1` uses more gas than `++i`** 

- line(s) in question:  [195](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol?plain=1#L195)

*Suggestion:* change [line 195](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol?plain=1#L195) from `marketIndex += 1` to `++marketIndex`.
***