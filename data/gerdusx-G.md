# Gas Optimizations
# [G-01] Short require/revert strings save gas 
Strings in solidity are handled in 32 byte chunks. A require/revert string longer than 32 bytes uses more gas. Shortening these strings will save gas.


There are 5 occurrences:

**PegOracle.sol** 

[L23](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L23) `require(_oracle1 != address(0), "oracle1 cannot be the zero address");`

[L24](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L24) `require(_oracle2 != address(0), "oracle2 cannot be the zero address");`


**StakingRewards.sol** 

[L217](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L217) `require(tokenAddress != address(stakingToken),"Cannot withdraw the staking token");`

[L226](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L226) `require(block.timestamp > periodFinish,"Previous rewards period must be complete before changing the duration for the new period");`


**SemiFungibleVault.sol** 

[L116](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L116) `require(msg.sender == owner || isApprovedForAll(owner, receiver),"Only owner can withdraw, or owner has approved receiver for all");`


#### Recommended Mitigation Steps
Shorten all require/revert strings to less than 32 characters


# [G-02] Use Custom Errors instead of revert()/require() 
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)


There are 19 occurrences:

**PegOracle.sol** 

[L23](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L23) `require(_oracle1 != address(0), "oracle1 cannot be the zero address");`

[L24](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L24) `require(_oracle2 != address(0), "oracle2 cannot be the zero address");`

[L25](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L25) `require(_oracle1 != _oracle2, "Cannot be same Oracle");`

[L28](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L28) `require((priceFeed1.decimals() == priceFeed2.decimals()),"Decimals must be the same");`

[L98](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L98) `require(price1 > 0, "Chainlink price <= 0");`

[L99](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L99) `require(answeredInRound1 >= roundID1,"RoundID from Oracle is outdated!");`

[L103](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L103) `require(timeStamp1 != 0, "Timestamp == 0 !");`

[L121](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L121) `require(price2 > 0, "Chainlink price <= 0");`

[L122](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L122) `require(answeredInRound2 >= roundID2,"RoundID from Oracle is outdated!");`

[L126](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L126) `require(timeStamp2 != 0, "Timestamp == 0 !");`


**StakingRewards.sol** 

[L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L96) `require(amount != 0, "Cannot stake 0");`

[L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119) `require(amount > 0, "Cannot withdraw 0");`

[L202](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L202) `require(rewardRate <= balance.div(rewardsDuration),"Provided reward too high");`

[L217](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L217) `require(tokenAddress != address(stakingToken),"Cannot withdraw the staking token");`

[L226](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L226) `require(block.timestamp > periodFinish,"Previous rewards period must be complete before changing the duration for the new period");`


**SemiFungibleVault.sol** 

[L91](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L91) `require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");`

[L116](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L116) `require(msg.sender == owner || isApprovedForAll(owner, receiver),"Only owner can withdraw, or owner has approved receiver for all");`


**Vault.sol** 

[L165](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L165) `require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");`

[L187](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187) `require(msg.value > 0, "ZeroValue");`


#### Recommended Mitigation Steps
Recommended to replace revert/require strings with custom errors.


