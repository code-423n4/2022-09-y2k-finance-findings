
| | issue |
| ----------- | ----------- |
| 1 | [state variables only set in the constructor should be declared immutable](#1-state-variables-only-set-in-the-constructor-should-be-declared-immutable) |
| 2 | [state variables should be cached in stack variables rather than re-reading them from storage](#2-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage) |
| 3 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#3-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 4 | [not using the named return variables when a function returns, wastes deployment gas](#4-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 5 | [`++i` costs less gas than `i++`, especially when it’s used in for-loops (--i/i-- too)](#5-i-costs-less-gas-than-i-especially-when-its-used-in-for-loops---ii---too) |
| 6 | [it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied](#6-it-costs-more-gas-to-initialize-non-constantnon-immutable-variables-to-zero-than-to-let-the-default-of-zero-be-applied) |
| 7 | [` ++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in](#7--ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops) |
| 8 | [require()/revert() strings longer than 32 bytes cost extra gas](#8-requirerevert-strings-longer-than-32-bytes-cost-extra-gas) |
| 9 | [using > 0 costs more gas than != 0 when used on a uint in a require() statement](#9-using--0-costs-more-gas-than--0-when-used-on-a-uint-in-a-require-statement) |
| 10 | [use custom errors rather than revert()/require() strings to save deployment gas](#10-use-custom-errors-rather-than-revertrequire-strings-to-save-deployment-gas) |
| 11 | [using `bool` for storage incurs overhead](#11-using-bool-for-storage-incurs-overhead) |
| 12 | [abi.encode() is less efficient than abi.encodepacked()](#12-abiencode-is-less-efficient-than-abiencodepacked) |
| 13 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#13-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 14 | [using private rather than public for constants, saves gas](#14-using-private-rather-than-public-for-constants-saves-gas) |
| 15 | [bytes constants are more efficient than string constants](#15-bytes-constants-are-more-efficient-than-string-constants) |


## 1. state variables only set in the constructor should be declared immutable

avoids a gsset (20000 gas)

- [Controller.sol#L13](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L13)

- [PegOracle.sol#L10](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L10)
- [PegOracle.sol#L11](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L11)
- [PegOracle.sol#L13](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L13)
- [PegOracle.sol#L15](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L15)
- [PegOracle.sol#L16](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L16)

- [SemiFungibleVault.sol#L20](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L20)
- [SemiFungibleVault.sol#L21](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L21)

- [StakingRewards.sol#L41](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L41)

- [RewardsFactory.sol#L9](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L9)
- [RewardsFactory.sol#L10](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L10)
- [RewardsFactory.sol#L11](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L11)


## 2. state variables should be cached in stack variables rather than re-reading them from storage

`asset`
- [Vault.sol#L189](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L189)

`treasury` and `controller` should be cached beforehand
- [VaultFactory.sol#L203](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L203)
- [VaultFactory.sol#L206](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L206)

`marketIndex` should be cached after line 195
- [VaultFactory.sol#L195](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195)

`rewardsDuration`
- [StakingRewards.sol#L190](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L190)

`admin`
- [RewardsFactory.sol#L100](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L100)

`govToken`
- [RewardsFactory.sol#L102](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L102)


## 3. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

- [VaultFactory.sol#L195](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195)


## 4. not using the named return variables when a function returns, wastes deployment gas

- [Controller.sol#L264](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L264)

- [PegOracle.sol#L49](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L49)
- [PegOracle.sol#L89](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L89)
- [PegOracle.sol#L112](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L112)

- [Vault.sol#L162](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L162)
- [Vault.sol#L185](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L185)
- [Vault.sol#L213](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L213)
- [Vault.sol#L263](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L263)
- [Vault.sol#L381](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L381)
- [Vault.sol#L448](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L448)

- [VaultFactory.sol#L186](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L186)
- [VaultFactory.sol#L388](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L388)

- [RewardsFactory.sol#L86](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L86)
- [RewardsFactory.sol#L148](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L148)


## 5. `++i` costs less gas than `i++`, especially when it’s used in for-loops (--i/i-- too)
Saves 6 gas per loop

- [Vault.sol#L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443)


## 6. it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied

- [Vault.sol#L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443)

- [VaultFactory.sol#L159](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L159)

- [StakingRewards.sol#L36](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L36)


## 7. ` ++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

- [Vault.sol#L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443)


## 8. require()/revert() strings longer than 32 bytes cost extra gas

- [PegOracle.sol#L23](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L23)
- [PegOracle.sol#L24](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L24)

- [SemiFungibleVault.sol#L116](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L116)

- [StakingRewards.sol#L217](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L217)
- [StakingRewards.sol#L226](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L226)


## 9. using > 0 costs more gas than != 0 when used on a uint in a require() statement

- [PegOracle.sol#L98](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L98)
- [PegOracle.sol#L121](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L121)

- [Vault.sol#L187](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187)


## 10. use custom errors rather than revert()/require() strings to save deployment gas

https://blog.soliditylang.org/2021/04/21/custom-errors/

all the `require()`s need this change


## 11. using `bool` for storage incurs overhead

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

- [Controller.sol#L277](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L277)

- [Vault.sol#L52](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L52)
- [Vault.sol#L54](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L54)
- [Vault.sol#L336](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L336)


## 12. abi.encode() is less efficient than abi.encodepacked()

- [RewardsFactory.sol#L118](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L118)
- [RewardsFactory.sol#L150](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L150)


## 13. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [Controller.sol#L292](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L292)
- [Controller.sol#L296](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L296)

- [PegOracle.sol#L13](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L13)
- [PegOracle.sol#L58](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L58)
- [PegOracle.sol#L62](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L62)
- [PegOracle.sol#L91](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L91)
- [PegOracle.sol#L95](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L95)
- [PegOracle.sol#L114](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L114)
- [PegOracle.sol#L118](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L118)


## 14. using private rather than public for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

- [Controller.sol#L16](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L16)


## 15. bytes constants are more efficient than string constants

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

- [SemiFungibleVault.sol#L20](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L20)
- [SemiFungibleVault.sol#L21](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L21)
- [SemiFungibleVault.sol#L66](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L66)
- [SemiFungibleVault.sol#L67](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L67)

- [Vault.sol#L116](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L116)
- [Vault.sol#L117](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L117)

- [VaultFactory.sol#L185](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L185)
