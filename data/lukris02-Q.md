# QA Report for Y2K Finance contest

## Overview
During the audit, 7 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
NC-1 | [Scientific notation may be used](#nc-1-scientific-notation-may-be-used) | Non-Critical | 4
NC-2 | [Constants may be used](#nc-2-constants-may-be-used) | Non-Critical | 7
NC-3 | [Missing NatSpec](#nc-3-missing-natspec) | Non-Critical | 16
NC-4 | [Open TODO](#nc-4-open-todo) | Non-Critical | 1
NC-5 | [Inconsistent use of named return variables](#nc-5-inconsistent-use-of-named-return-variables) | Non-Critical | 
NC-6 | [Public functions can be external](#nc-6-public-functions-can-be-external) | Non-Critical | 5
NC-7 | [Commented code](#nc-7-commented-code) | Non-Critical | 6

## Non-Critical Risk Findings (7)
### NC-1. Scientific notation may be used
##### Description
For readability, it is better to use scientific notation.

##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L68
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L70
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L78
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L266

##### Recommendation
Replace ```10000``` with ```10e4```.

#
### NC-2. Constants may be used
##### Description
Constants may be used instead of  literal values.

##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L68
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L70
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L78
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L138
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L266
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L311
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L389

##### Recommendation
Define constant variables, especially, for repeated values.

#
### NC-3. Missing NatSpec
##### Description
NatSpec is missing for 16 functions in 3 contracts.

##### Instances
- 2 functions in https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L276-L286
- 1 function in https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L268
- 13 functions in https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol

##### Recommendation
Add NatSpec for all functions.

#
### NC-4. Open TODO
##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L196

##### Recommendation
Resolve issues.

#
### NC-5. Inconsistent use of named return variables
##### Description
Some functions return named variables, others return explicit values.

##### Instances
For example:
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L115 
- and https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L137

#
### NC-6. Public functions can be external
##### Description
If functions are not called by the contract where they are defined, they can be declared external.

##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L148
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L198
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L46
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L89
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L145

##### Recommendation
Make public functions external, where possible.

#
### NC-7. Commented code
##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L268
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L270
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L49
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L51
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L53
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L392-L398

##### Recommendation
Delete commented code.