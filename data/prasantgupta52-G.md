# 1. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)
 ### Instances:
 // Links to github files
 [Vault.sol:L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443)
 ### Actual Codes Used:
```
src/Vault.sol:443:        for (uint256 i = 0; i < epochsLength(); i++) {
```
----
# 2.++I COSTS LESS GAS COMPARED TO I++ OR I += 1
*Pre-increments and pre-decrements are cheaper.*

For a `uint256` i variable, the following is true with the Optimizer enabled at 10k:
Increment:
`i += 1` is the most expensive form
`i++` costs `6` `gas` less than `i += 1`
`++i` costs `5 gas` less than `i++` (11 gas less than i += 1)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name post-increment:
### Instances
// Links to Github file
[Vault.sol:L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443)
[VaultFactory.sol:L195](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L195)
### Actual codes
```
src/Vault.sol:443:        for (uint256 i = 0; i < epochsLength(); i++) {
src/VaultFactory.sol:195:        marketIndex += 1;
```
----
# 3.Use != 0 instead of > 0 for Unsigned Integer Comparison

Using `> 0` uses slightly more gas than using `!= 0`. 
Use `!= 0` when comparing uint variables to zero, which cannot hold values below zero
When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance
Incidentally, after version 0.8.12, this optimization doesn’t work
You should change from `> 0` to  `!=0`.
### Instances
// Links to Github file
[Vault.sol:L187](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L187)
[PegOracle.sol:L98](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L98)
[PegOracle.sol:L121](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L121)
[StakingRewards.sol:L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L119)

### Actual codes
```
src/Vault.sol:187:        require(msg.value > 0, "ZeroValue");
src/oracles/PegOracle.sol:98:        require(price1 > 0, "Chainlink price <= 0");
src/oracles/PegOracle.sol:121:        require(price2 > 0, "Chainlink price <= 0");
src/rewards/StakingRewards.sol:119:        require(amount > 0, "Cannot withdraw 0");
```
----
# 4. Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use `uint number;` instead of `uint number = 0;`
 ### Instances:
 // Links to github files
[StakingRewards.sol:L36](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L36)
 ### Actual Codes Used:
```
src/rewards/StakingRewards.sol:36:    uint256 public periodFinish = 0;
```
### Recommended Mitigation Steps:
I suggest removing explicit initializations for default values.

----
# 5.Use Custom Errors instead of Revert Strings to save Gas
Custom errors from Solidity 0.8.4 are cheaper than revert strings 
(cheaper deployment cost and runtime cost when the revert condition is 
met)

Source [Custom Errors in Solidity](https://blog.soliditylang.org/2021/04/21/custom-errors/):

> Starting from Solidity v0.8.4,
 there is a convenient and gas-efficient way to explain to users why an 
operation failed through the use of custom errors. Until now, you could 
already use strings to give more information about failures (e.g., revert("Insufficient funds.");),
 but they are rather expensive, especially when it comes to deploy cost,
 and it is difficult to use dynamic information in them.
> 

Custom errors are defined using the `error` statement, which can be used inside and outside of contracts (including interfaces and libraries).
 ### Instances:
 // Links to github files
[Vault.sol:L165](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L165)
[Vault.sol:L187](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L187)
[PegOracle.sol:L23](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L23)
[PegOracle.sol:L24](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L24)
[PegOracle.sol:L25](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L25)
[PegOracle.sol:L98](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L98)
[PegOracle.sol:L103](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L103)
[PegOracle.sol:L121](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L121)
[PegOracle.sol:L126](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L126)
[SemiFungibleVault.sol:L91](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L91)
[StakingRewards.sol:L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L96)
[StakingRewards.sol:L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L119)
 ### Actual Codes Used:
```
src/Vault.sol:165:        require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
src/Vault.sol:187:        require(msg.value > 0, "ZeroValue");
src/oracles/PegOracle.sol:23:        require(_oracle1 != address(0), "oracle1 cannot be the zero address");
src/oracles/PegOracle.sol:24:        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
src/oracles/PegOracle.sol:25:        require(_oracle1 != _oracle2, "Cannot be same Oracle");
src/oracles/PegOracle.sol:98:        require(price1 > 0, "Chainlink price <= 0");
src/oracles/PegOracle.sol:103:        require(timeStamp1 != 0, "Timestamp == 0 !");
src/oracles/PegOracle.sol:121:        require(price2 > 0, "Chainlink price <= 0");
src/oracles/PegOracle.sol:126:        require(timeStamp2 != 0, "Timestamp == 0 !");
src/SemiFungibleVault.sol:91:        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
src/rewards/StakingRewards.sol:96:        require(amount != 0, "Cannot stake 0");
src/rewards/StakingRewards.sol:119:        require(amount > 0, "Cannot withdraw 0");
```
### Recommended Mitigation Steps:
replace revert strings with custom errors.

----
# 6. Use `abi.encodePacked()` not `abi.encode()`
### Description
Changing `abi.encode` to `abi.encodePacked` can save gas. `abi.encode` pads extra null bytes at the end of the call data which is normally unnecessary. In general, `abi.encodePacked` is more gas-efficient.
 ### Instances:
 // Links to github files
[RewardsFactory.sol:L118](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L118)
[RewardsFactory.sol:L150](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L150)
[RewardsFactory.sol:L118](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L118)
[RewardsFactory.sol:L150](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L150)
 ### Actual Codes Used:
```
src/rewards/RewardsFactory.sol:118:        bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));
src/rewards/RewardsFactory.sol:150:        return keccak256(abi.encode(_index, _epoch));
src/rewards/RewardsFactory.sol:118:        bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));
src/rewards/RewardsFactory.sol:150:        return keccak256(abi.encode(_index, _epoch));
```
### Recommended Mitigation Steps
You should change **abi.encode** to **abi.encodePacked**

----
# 7. Compairing Boolean value to True or False
Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here
 ### Instances:
 // Links to github files
[Vault.sol:L314](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L314)
[Controller.sol:L93](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L93)
[Controller.sol:L211](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L211)
[Vault.sol:L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L96)
[RewardsFactory.sol:L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L96)

 ### Actual Codes Used:
```
src/Vault.sol:314:        if(idExists[epochEnd] == true)
src/Controller.sol:93:        if(vault.idExists(epochEnd) == false)
src/Controller.sol:211:        if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
src/Vault.sol:96:        if((block.timestamp < id) && idDepegged[id] == false)
src/rewards/RewardsFactory.sol:96:        if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
```
----