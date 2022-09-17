
## 1. typo in comments 

disbable --> disable

- [PegOracle.sol#L85](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L85)
- [PegOracle.sol#L108](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L108)

transfered --> transferred

- [Vault.sol#L199](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L199)
- [Vault.sol#L415](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L415)

insrance --> insurance

- [RewardsFactory.sol#L25](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L25)
- [RewardsFactory.sol#L27](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L27)


## 2. event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

- [Controller.sol#L49](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L49)

- [SemiFungibleVault.sol#L35](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L35)
- [SemiFungibleVault.sol#L51](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L51)

- [VaultFactory.sol#L49](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L49)
- [VaultFactory.sol#L69](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L69)

- [StakingRewards.sol#L52](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L52)
- [StakingRewards.sol#L53](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L53)

- [RewardsFactory.sol#L39](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L39)


## 3. _safemint() should be used rather than _mint() wherever possible

- [SemiFungibleVault.sol#L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L96)

- [Vault.sol#L169](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L169)


## 4. constants should be defined rather than using magic numbers 

Even assembly can benefit from using readable constants instead of hex/numeric literals

- [Controller.sol#L299](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L299)

- [PegOracle.sol#L73](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L73)

- [Vault.sol#L311](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L311)

- [StakingRewards.sol#L168](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L168)
- [StakingRewards.sol#L177](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L177)


## 5. inconsistent spacing in comments

Some lines use // x and some use //x. The instances below point out the usages that don’t follow the majority, within each file

- [Controller.sol#L156](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L156)
- [Controller.sol#L214](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L214)
- [Controller.sol#L156](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L156)

- [Vault.sol#L225](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L225)
- [Vault.sol#L392](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L392)

- [VaultFactory.sol#L197](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L197)

- [RewardsFactory.sol#L25](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L25)


## 6. lines are too long

Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

- [Vault.sol#L198](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L198)
- [Vault.sol#L304](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L304)
- [Vault.sol#L346](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L346)
- [Vault.sol#L356](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L356)

- [VaultFactory.sol#L245](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L245)


## 7. open todos
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

- [Vault.sol#L196](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L196)


## 8. require() should be used instead of assert()
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert() do. assert() should be avoided even past solidity version 0.8.0 as its documentation states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

- [Vault.sol#L190](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190)


## 9. inconsistent use of named return variables

there is an inconsistent use of named return variables in the contract some functions return named variables, others return explicit values. consider adopting a consistent approach.
this would improve both the explicitness and readability of the code, and it may also help reduce regressions during future code refactors.
