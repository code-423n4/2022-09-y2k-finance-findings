# VaultFactory.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L178-L239
`VaultFactory.createNewMarket()` accesses many state variables multiple times. Gas can be saved in all cases by copying the state variable into a memory variable and then using the memory variable throughout the code.
`controller` is used on lines 188,192,206,216
`treasury` is used on lines 203,213
`marketIndex` is used on lines 195,219,226
(The first used of `marketIndex` modifies its value, but it can still be used to copy the new value into memory: `_marketIndex = marketIndex++;`

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L312
`VaultFactory.changeTreasury()` modifies both the state variable `VaultFactory.treasury` but also the treasury value of the specified market index. If the treasury values for multiple markets are to be changed, the state variable `treasury` will be written to multiple times.
Consider separating the function into two: one to change the state variable, the other to change the treasury within specified market vaults. This is the structure used in `setController()/changeController()`. 


# Controller.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L97-L99
Consider avoiding the second call to `getLatestPrice(vault.tokenInsured()` by saving its return value to a memory variable.

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L83-L110
In the modifier `isDisaster()`, gas can be saved by placing tests first which use the least gas and are the most likely to fail. This is a judgement call best made by the development team, but it may be the case that the last two tests are more likely to fail as they relate to human error. They are also relatively cheap to test. The third test also relates to human error, but is quite expensive. The first two tests need to remain early for logical reasons. Suggested order: 5,1,2,4,3

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L277
There is no need to create this `isSequencerUp` variable. Save gas with:
`if (answer != 0) {`
or
`if (answer == 1) {`
as line 278 and removing line 277.


# StakingRewards.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L109-L112
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L114
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L132
`exit()` calls the modifiers `nonReentrant updateReward(msg.sender)` twice. This burns extra gas for many users who are likely to use this function. Gas can be saved by creating internal functions with the functionality of `withdraw()` and `getReward()`. The external functions would then look something like this:
```
function exit() external nonReentrant updateReward(msg.sender) {
	_withdraw(_balances[msg.sender]);
	_getReward();
}
function withdraw(uint256 amount) external nonReentrant updateReward(msg.sender) {
	_withdraw(amount);
}
function getReward(uint256 amount) external nonReentrant updateReward(msg.sender) {
	_getReward(amount);
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L61
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L176
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L159-L171
`updateReward()` calls `earnings` and between them these functions call `rewardPerToken()` twice. Unfortunately `rewardPerToken()` is relatively a expensive function for retrieving a value as it involves some calculation. `updateReward()` is also a modifier that will be called frequently, so the impact of this issue could be quite high.
Consider writing out the logic of `updateReward()` to avoid the call to `earnings()` so that the return value of the first call to `rewardPerToken()` can be reused.

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L183-L210
`notifyRewardAmount()` accesses the state variable `rewardsDuration` multiple times. Gas can be saved by copying the state variable into a memory variable and then using the memory variable throughout the code.
`rewardsDuration` is used on lines 190,194,203,208.
