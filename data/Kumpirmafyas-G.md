Use != 0 instead of > 0
Using > 0 uses slightly more gas than using != 0. Use != 0 when comparing uint variables to zero, which cannot hold values below zero
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L134