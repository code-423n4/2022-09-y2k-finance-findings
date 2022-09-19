**No need to use OpenZeppelin’s `SafeMath` library if using Solidity >0.8**

[https://github.com/code-423n4/2022-09-y2k-finance/blob/8f41433c1cd866a138e6edbc32b7570fe94643fc/src/rewards/StakingRewards.sol#L4](https://github.com/code-423n4/2022-09-y2k-finance/blob/8f41433c1cd866a138e6edbc32b7570fe94643fc/src/rewards/StakingRewards.sol#L4)

`StakingRewards.sol` uses OpenZeppelin’s `SafeMath` library but it doesn’t need it as after solc 0.8 there is compiler built-in over/underflow checks.