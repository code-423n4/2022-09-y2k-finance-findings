# Report
## Gas Optimizations ## 

### [G-01]: X += Y costs more gas than X = X + Y

**Context:** 

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195

**Recommendation:**

Change X += Y (X -= Y) to X = X + Y (X = X - Y).

### [G-02]: Don't initialize variable with its default value 

**Context:** 

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

**Description:**

Default value of uint is 0. It's unnecessary and costs more gas to initialize uint variavles to 0.

**Recommendation:**

Change uint256 i = 0; to uint256 i;


### [G-03]: >0 costs more gas than !=0
**Context:** 

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119 uint256

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L134 uint256

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187

**Description:**

uint type and msg.value are always non-negative.

**Recommendation:**

Change >0 to !=0.

### [G-04]: Use new variable instead of calling function and reading array length in every loop of a for-loop

**Context:**

```
for (uint256 i = 0; i < epochsLength(); i++) {
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

**Description:**

If you call function and read the length of the array at each iteration of the loop, this consumes a lot of gas.


**Recommendation:**

Store the array’s length in a variable before the for-loop, and use this new variable in the loop.


### [G-05]: i++ costs more gas than ++i

**Context:**

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

**Recommendation:**

Change i++ to ++i.

### [G-06]: variable can be immutable
**Context:**

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L9

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L10

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L11

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L41

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L21

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L10

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L11

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L13

**Description:**

Variable is set in the constructor and never modified after that.

**Recommendation:**

It is more gas efficient to mark it as immutable.
