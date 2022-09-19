Gas Findings

## (1) Use pre increment instead of post increment

Pre-increment uses a little less gas.

***

for (uint256 i = 0; i < epochsLength(); i++) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L443

## (2) Use custom errors instead of strings for revert and require

Require strings and revert strings use about 50 more gas than custom errors.

***

require(_oracle1 != address(0), "oracle1 cannot be the zero address");
require(_oracle2 != address(0), "oracle2 cannot be the zero address");
require(_oracle1 != _oracle2, "Cannot be same Oracle");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L23-L25

require(price1 > 0, "Chainlink price <= 0");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L98

require(
    answeredInRound1 >= roundID1,
    "RoundID from Oracle is outdated!"
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L99-L102

require(timeStamp1 != 0, "Timestamp == 0 !");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L103

require(price2 > 0, "Chainlink price <= 0");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L121

require(
    answeredInRound2 >= roundID2,
    "RoundID from Oracle is outdated!"
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L122-L125

require(timeStamp2 != 0, "Timestamp == 0 !");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L126

require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L91

require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L165

require(msg.value > 0, "ZeroValue");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L187

require(amount != 0, "Cannot stake 0");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L96

require(amount > 0, "Cannot withdraw 0");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L119

require(
    block.timestamp > periodFinish,
    "Previous rewards period must be complete before changing the duration for the new period"
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L226-L229

## (3) Limit message strings for revert and require to 32 bytes

Require strings and revert strings longer than 32 bytes need MSTORE (3 gas).
Try to use shorter descriptions. Custom errors would be even better (see above).

***

require(_oracle1 != address(0), "oracle1 cannot be the zero address");
require(_oracle2 != address(0), "oracle2 cannot be the zero address");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L23-L24

require(
    msg.sender == owner || isApprovedForAll(owner, receiver),
    "Only owner can withdraw, or owner has approved receiver for all"
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L116-L119

require(
    tokenAddress != address(stakingToken),
    "Cannot withdraw the staking token"
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L217-L220

require(
    block.timestamp > periodFinish,
    "Previous rewards period must be complete before changing the duration for the new period"
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L226-L229

## (4) Boolean variables should not be used for storage

UINT256(1) for true and UINT256(2) for false would cost less gas than using boolean variables.

***

mapping(uint256 => bool) public idDepegged;
// @audit id can be uint32
mapping(uint256 => bool) public idExists;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L52-L54

## (5) Non-const variables should not be initialized to their default value.

Just using the default costs less gas.
For example use: uint256 abc;
Instead of: uint256 abc=0;

***

for (uint256 i = 0; i < epochsLength(); i++) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L443

uint256 public periodFinish = 0;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L36

## (6) Avoid ints or uints smaller than 256 bits

Using ints or uints smaller than 256 bits can cost extra gas since the EVM operates with 32 bytes at a time.
Consider using int256 or uint256. You can downcast them when necessary.

***

uint80 roundID,
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L292

uint80 roundID,
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L50

uint80 roundID1,
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L58

uint80 roundID1,
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L91

uint80 roundID2,
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L114

