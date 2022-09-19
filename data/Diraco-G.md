# When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. 
Use != 0 instead of > 0 at the above mentioned codes. The variable is uint, so it will not be below 0 so it can just check != 0.
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L134
# ++i costs less gas than i++, especially when it's used in for loops
++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443
