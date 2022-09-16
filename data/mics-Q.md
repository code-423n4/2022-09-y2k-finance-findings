Table Of Content
================

* [QA REPORT](#qa-report)
        * [Use timelock modifier for setter functions](#use-timelock-modifier-for-setter-functions)
        * [Array access is out of bounds](#array-access-is-out-of-bounds)
        * [Use safeTransfer() instead transfer()](#use-safetransfer-instead-transfer)
        * [Missing zero address check in a state variable setter function](#missing-zero-address-check-in-a-state-variable-setter-function)
        * [Missing 0 address check at transfer](#missing-0-address-check-at-transfer)
        * [Add event to the following functions](#add-event-to-the-following-functions)
        * [Consider adding constant variables instead of hardcoded strings](#consider-adding-constant-variables-instead-of-hardcoded-strings)
        * [Several functions are declaring named returns but then are using return statements. I suggest choosing only one for readability reasons.](#several-functions-are-declaring-named-returns-but-then-are-using-return-statements-i-suggest-choosing-only-one-for-readability-reasons)
        * [Not indexed events](#not-indexed-events)

# QA REPORT

## Use timelock modifier for setter functions
It is good to have a timelock for functions that set key/critical variables.

### Code Instances:
- [StakingRewards.sol#L225](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L225)
- [Vault.sol#L350](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L350)

## Array access is out of bounds
There is no check for the access to be in the array bounds.

For instance, [Vault.sol#L442](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L442)

## Use safeTransfer() instead transfer()
Use openzeppelin safeTransfer() method instead of transfer() in the following locations.

### Code Instances:
- [Vault.sol#L189](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L189)
- [Vault.sol#L230](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L230)
- [Vault.sol#L166](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L166)

## Missing zero address check in a state variable setter function
A state variable of type 'address' is set without a non-zero verification. This can lead to undesired behavior.

### Code Instances:
- [Owned.sol#L22](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/Owned.sol#L22)
- [RewardsFactory.sol#L67](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L67)
- [RewardsDistributionRecipient.sol#L23](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsDistributionRecipient.sol#L23)
- [Vault.sol#L277](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L277)

## Missing 0 address check at transfer
Some contracts does not support 0 transfer, then the transaction will revert with no explanation. We recommend to add a require statement that the amount is not 0.

For instance, [StakingRewards.sol#L220](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L220)

## Add event to the following functions


### Code Instances:
- [StakingRewards.sol#L80](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L80)
- [Vault.sol#L287](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L287)
- [StakingRewards.sol#L159](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L159)
- [Vault.sol#L295](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L295)

## Consider adding constant variables instead of hardcoded strings
A good practice is to use constant variables instead of hardcoded strings in the code.

### Code Instances:
- [StakingRewards.sol#L216](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L216)
- [StakingRewards.sol#L118](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L118)
- [PegOracle.sol#L120](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L120)
- [Vault.sol#L164](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L164)

## Several functions are declaring named returns but then are using return statements. I suggest choosing only one for readability reasons.
Using both named returns and a return statement isn't necessary. Removing one of those can improve code clarity.

### Code Instances:
- [RewardsFactory.sol#L87](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L87)
- [RewardsFactory.sol#L149](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L149)
- [VaultFactory.sol#L186](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L186)
- [Vault.sol#L264](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L264)

## Not indexed events
The emitted event is not indexed, making off-chain scripts such as front-ends of dApps difficult to filter the events efficiently.



### Code Instances:
- [StakingRewards.sol#L56](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L56)
- [StakingRewards.sol#L55](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L55)
- [StakingRewards.sol#L51](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L51)
- [Owned.sol#L8](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/Owned.sol#L8)

--