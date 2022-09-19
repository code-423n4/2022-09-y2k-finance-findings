# 1 Use != 0 instead of > 0 at the above mentioned codes. The variable is uint, so it will not be below 0 so it can just check != 0.
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled 
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L134
# 2 MULTIPLE ADDRESS MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS TO A STRUCT, WHERE APPROPRIATE
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L47
# 3  ( ++I/I++ ) SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
++I COSTS LESS GAS THAN I++
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443
# 4  ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L118
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L150