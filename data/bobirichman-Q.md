# QA REPORT

## [LOW] Consider replacing assert with require
Assertions are a bad practice, use require instead.

Example: [Vault.sol#L189](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L189)

## [LOW] The project is compiled with different solidity versions


## [LOW] Missing nonReentrancy modifier
The following functions allows attackers to try reentrancy since they are calling to external contracts / transferring eth. Consider adding a nonReentrancy modifier.

### Proof of concept:
- [Vault.sol#L214](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L214)
- [Controller.sol#L198](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L198)

## [LOW] Not verified input
At the following functions you should verify the parameters that are being assigned to a state variable.

### Proof of concept:
- [VaultFactory.sol#L295](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L295)
- [RewardsFactory.sol#L67](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L67)

## [LOW] Approve 0 first
At some tokens you can approve an amount (at USDT for instance) only after approving to 0. Consider using increase/decrease approve notation instead.

Example: [SemiFungibleVault.sol#L115](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L115)

## [LOW] In the following functions consider verifying the fee parameter
Where the fee parameter validation is checking greater than 0% (which may happen by mistake) and less than 100%

### Proof of concept:
- [VaultFactory.sol#L186](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L186)
- [VaultFactory.sol#L253](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L253)

## [LOW] Use safeTransfer
Use safeTransfer in the following locations

### Proof of concept:
- [Vault.sol#L189](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L189)
- [Vault.sol#L230](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L230)

## [LOW] Missing pause functionality


### Proof of concept:
- [Vault.sol](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol)
- [SemiFungibleVault.sol](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol)

## [NON CRITICAL] Missing event emit
In functions that update/assigns state variables it is a good practice to emit event.

### Proof of concept:
- [PegOracle.sol#L22](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L22)
- [Controller.sol#L125](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L125)

## [NON CRITICAL] Missing function spec comments


### Proof of concept:
- [VaultFactory.sol#L295](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L295)
- [Controller.sol#L151](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L151)

## [NON CRITICAL] NonReentrancy should be the first modifier in order


Example: [Vault.sol#L163](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L163)

## [NON CRITICAL] Consider emitting an event at the following functions


### Proof of concept:
- [RewardsFactory.sol#L67](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L67)
- [VaultFactory.sol#L253](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L253)

## [NON CRITICAL] The following events are not indexed


### Proof of concept:
- [StakingRewards.sol#L56](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L56)
- [Controller.sol#L49](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L49)

--