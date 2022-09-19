# Gas Optimizations Report for Y2k Finance contest

## Overview
During the audit, 7 gas issues were found.

№ | Title | Instance Count
--- | --- | --- 
G-1 | [Postfix increment](#g-1-postfix-increment) | 1
G-2 | [<>.length in loops](#g-2-length-in-loops) | 1
G-3 | [Initializing variables with default value](#g-3-initializing-variables-with-default-value) | 1
G-4 | [> 0 is more expensive than =! 0](#g-4--0-is--more-expensive-than--0) | 3
G-5 | [x += y is more expensive than x = x + y](#g-5-x--y-is--more-expensive-than-x--x--y) | 1
G-6 | [Using unchecked blocks saves gas](#g-6-using-unchecked-blocks-saves-gas) | 1
G-7 | [Some variables can be immutable](#g-7-some-variables-can-be-immutable) | 7

## Gas Optimizations Findings (7)
### G-1. Postfix increment
##### Description
Prefix increment costs less gas than postfix.

##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

##### Recommendation
Consider using prefix increment  where it is relevant. 

#
### G-2. <>.length in loops
##### Description
Reading the length of an array at each iteration of the loop consumes extra gas.

##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

##### Recommendation
Store the length of an array in a variable before the loop, and use it.

#
### G-3. Initializing variables with default value
##### Description
It costs gas to initialize integer variables with 0 or bool variables with false but it is not necessary.

##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

##### Recommendation
Remove initialization for default values.  
For example:
``` for (uint256 i; i < array.length; ++i) {```

#
### G-4. ```> 0``` is  more expensive than ```=! 0```
##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119 
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L134 
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187

##### Recommendation
Use ```=! 0``` instead of ```> 0```, where possible.

#
### G-5. ```x += y``` is  more expensive than ```x = x + y```
##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195

##### Recommendation
Use ```x = x + y``` instead of ```x += y```.
Use ```x = x - y``` instead of ```x -= y```.

#
### G-6. Using unchecked blocks saves gas
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible, some gas can be saved by using unchecked blocks.

##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```

#
### G-7. Some variables can be immutable
##### Description
Using immutables is cheaper than storage-writing operations.

##### Instances
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L10
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L11
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L13
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L41
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L9
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L10
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L11

##### Recommendation
Use immutables where possible.  