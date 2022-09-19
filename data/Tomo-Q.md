# QA Report

## ✅ L-1: `require()` should be used instead of `assert()`

### 📝 Description

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix

### 💡 Recommendation

You should change from `assert()` to `require()`

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/Vault.sol#L190` [assert(IWETH(address(asset)).transfer(msg.sender, msg.value));](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190)


## ✅ N-1: Open Todos

### 📝 Description

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

### 💡 Recommendation

Delete TODO keyword

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/Vault.sol#L196` [@notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L196)

## ✅ N-2: No use of two-phase ownership transfers

### 📝 Description

Consider adding a two-phase transfer, where the current owner nominates the next owner, and the next owner has to call `accept*()` to become the new owner. This prevents passing the ownership to an account that is unable to use it.

### 💡 Recommendation

Consider implementing a two step process where the admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of admin to fully succeed. This ensures the nominated EOA account is a valid and active account.

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/Controller.sol#L135` [admin = \_admin;](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L135)

`2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L68` [admin = \_admin;](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L68)

## ✅ N-3: Use `string.concat()` or`bytes.concat()`

### 📝 Description

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)Solidity version 0.8.12 introduces `string.concat()`(vs `abi.encodePacked(<str>,<str>)`)

### 💡 Recommendation

Use concat instead of abi.encodePacked

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/Controller.sol#L180` [abi.encodePacked(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L180)

`2022-09-y2k-finance/blob/main/src/Controller.sol#L236` [abi.encodePacked(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L236)

`2022-09-y2k-finance/blob/main/src/Vault.sol#L388` [keccak256(abi.encodePacked(symbol)) ==](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L388)

`2022-09-y2k-finance/blob/main/src/Vault.sol#L389` [keccak256(abi.encodePacked("rY2K"))](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L389)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L201` [string(abi.encodePacked(\_name,"HEDGE")),](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L201)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L211` [string(abi.encodePacked(\_name,"RISK")),](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L211)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L278` [keccak256(abi.encodePacked(\_marketVault.index, \_marketVault.epochBegin, \_marketVault.epochEnd)),](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L278)

`2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L126` [abi.encodePacked(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L126)
