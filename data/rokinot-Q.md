## Low

### Market index 0 is skipped

The first market index is number 1, rather than 0.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195

## Non-critical

### Open `TODO` comment

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L196

### `StakingRewards.sol` is using the SafeMath library

Standard mathematical operations reverts if an invalid operation occurs in solidity ^0.8, which means the library is deprecated.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L4

### Missing _name dev parameter

Function takes 7 arguments, however the @dev comment for `_name` is missing

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L169-L174