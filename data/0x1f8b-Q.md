- [Low](#low)
    - [**1. Denial of service with tokens with > 18 decimals**](#1-denial-of-service-with-tokens-with--18-decimals)
    - [**2. Lack of checks**](#2-lack-of-checks)
    - [**3. Uninsured Rewards**](#3-uninsured-rewards)
    - [**4. Revert if the data is incorrect**](#4-revert-if-the-data-is-incorrect)
    - [**5. Lack of event emit**](#5-lack-of-event-emit)
- [Non critical](#non-critical)
    - [**6. Outdated compiler**](#6-outdated-compiler)
    - [**7. Invert conditions**](#7-invert-conditions)
    - [**8. Reuse code to avoid errors**](#8-reuse-code-to-avoid-errors)
    - [**9. Modifier should not modify states**](#9-modifier-should-not-modify-states)
    - [**10. Unused method which may incur inheritance issues**](#10-unused-method-which-may-incur-inheritance-issues)

# Low

## **1. Denial of service with tokens with > 18 decimals**

`PegOracle` contract assume decimals <= 18 and does not handle > 18 decimals.

Because the pragma used doesn't allow integer underflows, if a token with more than 18 decimals is used, an integer underflow will produce a denial of service.

Some tokens have more than 18 decimals (e.g. YAM-V2 has 24).

This may trigger unexpected reverts due to overflow, posing a liveness risk to the contract.

**Reference:**

Major severity finding from Consensys Diligence Audit of Defi Saver:
- https://consensys.net/diligence/audits/2021/03/defi-saver/#tokens-with-more-than-18-decimal-points-will-cause-issues

**Affected source code:**

- [PegOracle.sol:36](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L36)
- [PegOracle.sol:73](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L73)

## **2. Lack of checks**

The following methods have a lack of checks if the received argument is an address, it's good practice in order to reduce human error to check that the address specified in the constructor or initialize is different than `address(0)`.

**Affected source code for `address(0)`:**

- [RewardsFactory.sol:64-66](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L64-L66)
- [StakingRewards.sol:74-76](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L74-L76)
- [SemiFungibleVault.sol:70](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L70)

**Affected source code for integer range checks:**

- [StakingRewards.sol:77-79](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L77-L79)

**`_withdrawalFee` must be less than the factor:**

- [VaultFactory.sol:179](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L179)
- [VaultFactory.sol:252](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L252)

## **3. Uninsured Rewards**

Nothing ensures that there are rewards for the users stakes, it can also be stolen from the `rewardsToken` by the admin.

**Affected source code:**

- [StakingRewards.sol:136](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L136)

## **4. Revert if the data is incorrect**

The admin might think that the change has been applied and it is not. If it shouldn't call `changeOracle` and maybe due to human error, the admin won't.

```diff
        if (tokenToOracle[_token] == address(0)) {
            tokenToOracle[_token] = _oracle;
        }
+       else if (tokenToOracle[_token] != _oracle) {
+           revert();
+       }
```

**Affected source code:**

- [VaultFactory.sol:223](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L223)

## **5. Lack of event emit**

The `setClaimTVL`, `changeTreasury`, `changeTimewindow` and `changeController` methods do not emit an event when the state changes, something that it's very important for dApps and users.

**Affected source code:**

- [Vault.sol:277-299](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L277-L299)
- [Vault.sol:351](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L351)

---

# Non critical

## **6. Outdated compiler**

The pragma version used are:

```
pragma solidity 0.8.15;
```

*Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.*

The minimum required version must be [0.8.16](https://github.com/ethereum/solidity/releases/tag/v0.8.16); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

Apart from these, there are several minor bug fixes and improvements.

## **7. Invert conditions**

To make it easier to detect the error, these conditions should be reversed, otherwise the user will receive a different error.

```diff
+       if(controller == address(0))
+           revert ControllerNotSet();
        if(
            IController(controller).getVaultFactory() != address(this)
            )
            revert AddressFactoryNotInController();

-       if(controller == address(0))
-           revert ControllerNotSet();
```

**Affected source code:**

- [VaultFactory.sol:187-193](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L187-L193)

## **8. Reuse code to avoid errors**

The `RewardsFactory` contract replicates the logic of the `getHashedIndex` method in some places, it is convenient to call the method since if said calculation is updated, it will have to be changed in several places with the possible human error that this entails.

**Affected source code:**

Call `getHashedIndex` instead replicate the logic in:

- [RewardsFactory.sol#L118](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L118)

## **9. Modifier should not modify states**

It is not recommended that a modifier modify the states of the contract, normally they can be called multiple times by chaining different calls, and it also complicates the readability and auditability of the code, so it is recommended to use any modifier only for validations.

**Affected source code:**

- [StakingRewards.sol:60](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L60)

## **10. Unused method which may incur inheritance issues**

In `SemiFungibleVault` contract, the methods `maxDeposit`, `maxMint` and `maxWithdraw` are not used, if someone understands the contract and modifies these virtual methods, it will not be applied unless they also modify the `deposit` and `withdraw` methods.

**Affected source code:**

- [SemiFungibleVault.sol:237-258](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L237-L258)
