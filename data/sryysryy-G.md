## `++I` COSTS LESS GAS THAN `I++`, ESPECIALLY WHEN IT’S USED IN `FOR`-LOOPS (`--I`/`I--` TOO)
*There is 1 instance of this issue*
```solidity
src/Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443
&nbsp;
&nbsp;

##  Don't Initialize Variables with Default Value
*There is 1 instance of this issue*
```solidity
src/Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443
&nbsp;
&nbsp;

##  COMPARISONS: != IS MORE EFFICIENT THAN > IN REQUIRE 
*There are 4 instances of this issue*
```solidity
PegOracle.sol::98 => require(price1 > 0, "Chainlink price <= 0");
PegOracle.sol::121 => require(price2 > 0, "Chainlink price <= 0");
StakingRewards.sol::119 => require(amount > 0, "Cannot withdraw 0");
Vault.sol::187 => require(msg.value > 0, "ZeroValue");
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L98
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L121
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119

&nbsp;
&nbsp;


##  USING BOOLS FOR STORAGE INCURS OVERHEAD
*There are 2 instances of this issue*
```solidity
Vault.sol::52 =>     mapping(uint256 => bool) public idDepegged;
Vault.sol::54 =>     mapping(uint256 => bool) public idExists;
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L52
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L54
&nbsp;
&nbsp;

## MULTIPLE `ADDRESS` MAPPINGS CAN BE COMBINED INTO A SINGLE `MAPPING` OF AN `ADDRESS` TO A `STRUCT`, WHERE APPROPRIATE
*There are 4 instances of this issue*
```solidity
rewards/StakingRewards.sol::43 =>    mapping(address => uint256) public userRewardPerTokenPaid;
rewards/StakingRewards.sol::44 =>    mapping(address => uint256) public rewards;
rewards/StakingRewards.sol::47 =>    mapping(address => uint256) private _balances;
VaultFactory.sol::121 =>    mapping(address => address) public tokenToOracle;
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L43
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L44
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L47

&nbsp;
&nbsp;

## USING `PRIVATE` RATHER THAN `PUBLIC` FOR CONSTANTS, SAVES GAS
*There is 1 instance of this issue*
```solidity
Controller.sol::16 =>    uint256 public constant VAULTS_LENGTH = 2;
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L16

