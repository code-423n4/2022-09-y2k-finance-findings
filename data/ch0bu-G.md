## 1. `++i` costs less gas compared to `i++` or `i += 1`, same for `--i/i--`. Especially in for loops

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments i and returns the initial value of `i`.

```
uint i = 1;  
i++; // == 1 but i == 2
```
But ++i returns the actual incremented value:
```
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

I suggest using ++i instead of i++ to increment the value of an uint variable.

If done inside for loop, saves 6 gas per loop.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol
```
443		for (uint256 i = 0; i < epochsLength(); i++) {
```

## 2. No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: `for (uint256 i = 0; i < epochsLength(); ++i) {` should be replaced with for `(uint256 i; i < epochsLength(); ++i) {`

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol
```
443		for (uint256 i = 0; i < epochsLength(); i++) {
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol
```
36		 uint256 public periodFinish = 0;
```

## 3. Use custom errors instead of revert strings to save Gas

Custom errors from Solidity 0.8.4 are cheaper than revert string (cheaper deployment cost and runtime cost when the revert condition is met)

Source: https://blog.soliditylang.org/2021/04/21/custom-errors/:

> Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol
```
91		require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
...
116		require(
117        		 msg.sender == owner || isApprovedForAll(owner, receiver),
118         	         "Only owner can withdraw, or owner has approved receiver for all"
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol
```
165		require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
187		require(msg.value > 0, "ZeroValue");
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol
```
23		require(_oracle1 != address(0), "oracle1 cannot be the zero address");
24      	require(_oracle2 != address(0), "oracle2 cannot be the zero address");
25      	require(_oracle1 != _oracle2, "Cannot be same Oracle");
...
28		require(
29                    (priceFeed1.decimals() == priceFeed2.decimals()),
30                    "Decimals must be the same"
...
98		require(price1 > 0, "Chainlink price <= 0");
99		require(
100                 answeredInRound1 >= roundID1,
101                 "RoundID from Oracle is outdated!"
102	        );
103		require(timeStamp1 != 0, "Timestamp == 0 !");
...
121		require(price2 > 0, "Chainlink price <= 0");
122             require(
123                  answeredInRound2 >= roundID2,
124                 "RoundID from Oracle is outdated!"
125             );
126             require(timeStamp2 != 0, "Timestamp == 0 !");
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol
```
96		require(amount != 0, "Cannot stake 0");
119		require(amount > 0, "Cannot withdraw 0");
...
202		require(
203                  rewardRate <= balance.div(rewardsDuration),
204                 "Provided reward too high"
...
217		require(
218                tokenAddress != address(stakingToken),
219                "Cannot withdraw the staking token"
...
226		require(
227                 block.timestamp > periodFinish,
228                 "Previous rewards period must be complete before changing the duration for the new period"
```

# 4. \<x\> += \<y\> costs more gas than \<x\> = \<x\> + \<y\> for state variables

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol
```
195		marketIndex += 1;
```

## 5. Using > 0 costs more gas than != 0 for Unsigned Integers

This change saves 6 gas per instance

Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proofs: https://twitter.com/gzeon/status/1485428085885640706

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol
```
120		require(amount > 0, "Cannot withdraw 0");
```

## 6. require()/revert() strings longer than 32 bytes cost extra gas

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol
```
116		require(
117                msg.sender == owner || isApprovedForAll(owner, receiver),
118                "Only owner can withdraw, or owner has approved receiver for all"
119           );
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol
```
23		require(_oracle1 != address(0), "oracle1 cannot be the zero address");
24              require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol
```
217		require(
218                 tokenAddress != address(stakingToken),
219                "Cannot withdraw the staking token"
...
226		require(
227                block.timestamp > periodFinish,
228                "Previous rewards period must be complete before changing the duration for the new period"
```

## 7. Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Wherever possible use uint256/int256 instead of smaller types.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol
```
292		uint80 roundID,
296		uint80 answeredInRound
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol
```
13		uint8 public decimals;
50		uint80 roundID,
54		uint80 answeredInRound
58		uint80 roundID1,
62		uint80 answeredInRound1
```

## 8. Using bools for storage incurs overhead


// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and 
// pointer aliasing, and it cannot be disabled.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

Use uint256(1) and uint256(2) for true/false

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol
```
52		bool isDisaster,
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol
```
52		mapping(uint256 => bool) public idDepegged;
54		mapping(uint256 => bool) public idExists;
```

## 9. Not using the named return variables when a function returns, wastes deployment gas

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol
```
181		return convertToShares(id, assets);
227		return convertToAssets(id, shares);
238		return type(uint256).max;
245		return type(uint256).max;
257		return convertToAssets(id, balanceOf(owner, id));
269		return balanceOf(owner, id);
```	
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol
```
192		return deposit(id, msg.value, receiver);
251		return totalSupply(_id);
266		return (amount * epochFee[_epoch]) / 1000;
431		return epochs.length;
448		return epochs[i + 1];
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol
```
390		return indexVaults[index];
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol
```
148		return _balances[account];
152		return rewardRate.mul(rewardsDuration);
```