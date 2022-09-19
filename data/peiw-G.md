- Don't use safemath once the solidity version is 0.8.0 or greater.
	- Version 0.8.0 introduces internal overflow checks, so using SafeMath is redundant and adds overhead.
	- Instance:
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L4

- `++i` costs less gas than `i++`, especially when in FOR loops.
	- `++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration).
	- Instance: 
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443


- Using unchecked blocks to save gas - increments in for loop can be unchecked (SAVE 30-40 GAS PER LOOP ITERATION).
	- The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop .
	- Instance: 
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

-  It costs more gas to initialize NON-CONSTANT/NON-IMMUTABLE variables to zero than to let the default of zero be applied.
	- Instance: 
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L36
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L159


- `<ARRAY>.length` should not be looked up in every loop of a for-loop.
	- Instance: 
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443


- `ABI.ENCODE()` is less efficient than `ABI.ENCODEPACKED()`.
	- Instance: 
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L118
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L150


- Usage of `UINTS/INTS` smaller than 32 bytes incurs overhead.
	-  When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed.
	- Instances:
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L292
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L296


- `!=` is more efficient than `>` in `require()`.
	- Instance:
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187


- `x += y` cost more gas than `x = x + y` for state variables.
	- Instance:
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195


- Don't compare boolean expression to boolean literals.
	- Instances:
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L314
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L96


- Using `PRIVATE` rather than `PUBLIC` for `CONSTANT`, saves gas.
	- Instance:
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L296


- Use custom errors rather than `REVERT()/REQUIRE()` strings to save gas.
	- Custom errors are available from solidity version 0.8.4. Custom errors save \~50 gas each time, they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.
	- Instances:
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L165
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L202
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L217
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L226
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsDistributionRecipient.sol#L11
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L91
		- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L116
