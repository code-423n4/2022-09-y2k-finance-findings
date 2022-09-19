# Gas | QA

## ✅ G-1: Don't Initialize Variables with Default Value

### 📝 Description

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas. Not overwriting the default for [stack variables](https://gist.github.com/IllIllI000/e075d189c1b23dce256cd166e28f3397) saves 8 gas. Storage and memory variables have larger savings

### 💡 Recommendation

Delete useless variable declarations to save gas.

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/Vault.sol#L443` [for (uint256 i = 0; i < epochsLength(); i++) {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443)

## ✅ G-2: Use != 0 instead of > 0 for Unsigned Integer Comparison

### 📝 Description

Use != 0 when comparing uint variables to zero, which cannot hold values below zero

### 💡 Recommendation

You should change from `> 0` to `!=0`.

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/Vault.sol#L187` [require(msg.value > 0, "ZeroValue");](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187)

`2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L98` [require(price1 > 0, "Chainlink price <= 0");](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L98)

`2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L121` [require(price2 > 0, "Chainlink price <= 0");](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L121)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119` [require(amount > 0, "Cannot withdraw 0");](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L134` [if (reward > 0) {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L134)

## ✅ G-3: Long Revert Strings

### 📝 Description

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

### 💡 Recommendation

Use short statements instead of long statement.

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L118` ["Only owner can withdraw, or owner has approved receiver for all"](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L118)

`2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L23` [require(\_oracle1 != address(0), "oracle1 cannot be the zero address");](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L23)

`2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L24` [require(\_oracle2 != address(0), "oracle2 cannot be the zero address");](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L24)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L8` [} from "@openzeppelin/contracts/security/ReentrancyGuard.sol";](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L8)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L16` [} from "@openzeppelin/contracts/token/ERC1155/utils/ERC1155Holder.sol";](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L16)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L219` ["Cannot withdraw the staking token"](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L219)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L228` ["Previous rewards period must be complete before changing the duration for the new period"](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L228)

## ✅ G-4: Use assembly to hash

### 📝 Description

See [this issue](https://github.com/code-423n4/2022-06-putty-findings/issues/59)
You can save about 5000 gas per instance in deploying contracrt.
You can save about 80 gas per instance if using assembly to execute the function.

### 💡 Recommendation

Please follow [this link](https://gist.github.com/Tomosuke0930/72beb469f08c954b232e58b8da03ef28) to make corrections

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/Controller.sol#L179` [keccak256(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L179)

`2022-09-y2k-finance/blob/main/src/Controller.sol#L235` [keccak256(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L235)

`2022-09-y2k-finance/blob/main/src/Vault.sol#L388` [keccak256(abi.encodePacked(symbol)) ==](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L388)

`2022-09-y2k-finance/blob/main/src/Vault.sol#L389` [keccak256(abi.encodePacked("rY2K"))](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L389)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L278` [keccak256(abi.encodePacked(\_marketVault.index, \_marketVault.epochBegin, \_marketVault.epochEnd)),](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L278)

`2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L118` [bytes32 hashedIndex = keccak256(abi.encode(\_marketIndex, \_epochEnd));](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L118)

`2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L125` [keccak256(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L125)

`2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L150` [return keccak256(abi.encode(\_index, \_epoch));](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L150)

## ✅ G-5: Don't use SafeMath lib 0.8.0~

### 📝 Description

Version 0.8.0~, it can calculate as a `SafeMath` without using it.

### 💡 Recommendation

Delete the import to save gas.

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L4` [import {SafeMath} from "@openzeppelin/contracts/utils/math/SafeMath.sol";](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L4)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L29` [using SafeMath for uint256;](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L29)

## ✅ G-6: Functions guaranteed to revert when called by normal users can be marked payable

### 📝 Description

See [this issue](https://github.com/code-423n4/2022-06-putty-findings/issues/59)
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
Saves 2400 gas per instance in deploying contracrt.
Saves about 20 gas per instance if using assembly to executing the function.

### 💡 Recommendation

You should add the keyword `payable` to those functions.

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/Vault.sol#L277` [function changeTreasury(address \_treasury) public onlyFactory {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L277)

`2022-09-y2k-finance/blob/main/src/Vault.sol#L287` [function changeTimewindow(uint256 \_timewindow) public onlyFactory {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L287)

`2022-09-y2k-finance/blob/main/src/Vault.sol#L295` [function changeController(address \_controller) public onlyFactory {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L295)

`2022-09-y2k-finance/blob/main/src/Vault.sol#L309` [onlyFactory](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L309)

`2022-09-y2k-finance/blob/main/src/Vault.sol#L338` [onlyController](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L338)


`2022-09-y2k-finance/blob/main/src/Vault.sol#L350` [function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L350)

`2022-09-y2k-finance/blob/main/src/Vault.sol#L362` [onlyController](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L362)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L186` [) public onlyAdmin returns (address insr, address rsk) {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L186)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L253` [) public onlyAdmin {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L253)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L295` [function setController(address \_controller) public onlyAdmin {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L295)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L310` [onlyAdmin](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L310)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L329` [onlyAdmin](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L329)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L347` [onlyAdmin](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L347)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L366` [function changeOracle(address \_token, address \_oracle) public onlyAdmin {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L366)


`2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L85` [onlyAdmin](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L85)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L186` [onlyRewardsDistribution](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L186)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L215` [onlyOwner](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L215)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L225` [function setRewardsDuration(uint256 \_rewardsDuration) external onlyOwner {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L225)


## ✅ G-7: Use `++i` instead of `i++`

### 📝 Description

You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting
The post-increment operation (i++) boils down to saving the original value of i, incrementing it and saving that to a temporary place in memory, and then returning the original value; only after that value is returned is the value of i actually updated (4 operations).On the other hand, in pre-increment doesn't need to store temporary(2 operations) so, the pre-increment operation uses less opcodes and is thus more gas efficient.

### 💡 Recommendation

You should change from i++ to ++i.

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/Vault.sol#L443` [for (uint256 i = 0; i < epochsLength(); i++) {](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443)

## ✅ G-8: Use x=x+y instad of x+=y

### 📝 Description

You can save about 35 gas per instance if you change from `x+=y**`\*\* to `x=x+y`
This works equally well for subtraction, multiplication and division.

### 💡 Recommendation

You should change from `x+=y` to `x=x+y`.

### 🔍 Findings:

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195` [marketIndex += 1;](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195)

## ✅ G-9: Use `indexed` for uint, bool, and address

### 📝 Description

Using the indexed keyword for value types such as uint, bool, and address saves gas costs, as seen in the example below. However, this is only the case for value types, whereas indexing bytes and strings are more expensive than their unindexed version.
Also indexed keyword has more merits.
It can be useful to have a way to monitor the contract's activity after it was deployed. One way to accomplish this is to look at all transactions of the contract, however that may be insufficient, as message calls between contracts are not recorded in the blockchain. Moreover, it shows only the input parameters, not the actual changes being made to the state. Also events could be used to trigger functions in the user interface.

### 💡 Recommendation

Up to three `indexed` can be used per event and should be added.

### 🔍 Findings:


`2022-09-y2k-finance/blob/main/src/Controller.sol#L49` [event DepegInsurance(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L49)

`2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L35` [event Deposit(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L35)

`2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L51` [event Withdraw(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L51)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L49` [event MarketCreated(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L49)


`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L69` [event EpochCreated(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L69)


`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L85` [event controllerSet(address indexed newController);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L85)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L91` [event changedTreasury(address \_treasury, uint256 indexed \_marketIndex);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L91)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L97` [event changedVaultFee(uint256 indexed \_marketIndex, uint256 \_feeRate);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L97)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L103` [event changedTimeWindow(uint256 indexed \_marketIndex, uint256 \_timeWindow);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L103)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L109` [event changedController(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L109)

`2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L113` [event changedOracle(address indexed \_token, address \_oracle);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L113)

`2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L39` [event CreatedStakingReward(](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L39)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L51` [event RewardAdded(uint256 reward);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L51)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L52` [event Staked(address indexed user, uint256 id, uint256 amount);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L52)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L53` [event Withdrawn(address indexed user, uint256 id, uint256 amount);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L53)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L54` [event RewardPaid(address indexed user, uint256 reward);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L54)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L55` [event RewardsDurationUpdated(uint256 newDuration);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L55)

`2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L56` [event Recovered(address token, uint256 amount);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L56)
