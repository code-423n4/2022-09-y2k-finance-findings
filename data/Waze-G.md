#1 Visibility 

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L16

change visibility from public to private or internal can save the gas.

#2 Loop

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443

default uint is 0 so remove unnecassary explicit can reduce gas.
caching the array length can reduce gas it caused access to a local variable is more cheap than query storage / calldata / memory in solidity.
pre increment e.g ++i more cheaper gas than post increment e.g i++. i suggest to use pre increment.

#3 Use  x = x + y or  x = x - y more cheap than x += y or x -= y for state variables

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L195

Change the state to x += y or x -= y for gas efficiency

#4 use !=0 instead >0 for unsigned integer

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L119

for unsigned integer, >0 is less efficient then !=0, so use !=0 instead of >0.

#5 Short revert message

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L228

reduce string size of error message to bytes32 can reduce the gas fee. reduce these when possible.