USE OF BLOCK.TIMESTAMP
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L189
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L245
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L283
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L189

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

Proof of Concept
Navigate to the following contract.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol


Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.