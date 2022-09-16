## setRewardsDuration allows 0 duration

Contract:
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L225

Issue:
The contract currently allows Owner to set duration as 0 which means notifyRewardAmount will always fail (division by zero). Also there seems to be no min/max cap on reward duration which highly impacts how much rewards are distributed

```
rewardRate = reward.div(rewardsDuration);
```

Recommendation:
Do not allow 0 reward duration and also set a min/max cap for reward duration

## No check to see if stakingToken==rewardsToken

Contract:
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L82

Issue:
The constructor has currently no check to see if stakingToken==rewardsToken. This could cause blunder where user staked token will be sent out as rewards

Recommendation:
Add below check in constructor

```
require(_stakingToken!=_rewardsToken);
```

## No market exist on index 0

Contract:
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195

Issue:
In createNewMarket function, The first market index starts with 1 as seen at VaultFactory.sol#L195. However checks in function like deployMoreAssets fails to consider this and only checks index > marketIndex which means 0 will be considered as valid market index when it is not

Recommendation:
Consider adding one more check index!=0 to verify for correct market index