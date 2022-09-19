### Don't Initialize Variables with Default Value
Uninitialized variables are assigned with the types default value.
Explicitly initializing a variable with it's default value costs unnecesary gas.

There are 1 instances of this issue:
```
File: src/Vault.sol

443:        for (uint256 i = 0; i < epochsLength(); i++) {
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443


### COMPARISONS: != IS MORE EFFICIENT THAN > IN REQUIRE (6 GAS LESS)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas).

For uints the minimum value would be 0 and never a negative value. Since it cannot be a negative value, then the check > 0 is essentially checking that the value is not equal to 0 therefore >0 can be replaced with !=0 which saves gas.

Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proofs: https://twitter.com/gzeon/status/1485428085885640706

I suggest changing > 0 with != 0 here:

File: src\Vault.sol [Line 187](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L187)
```
          require(msg.value > 0, "ZeroValue");
```
File: src\oracles\PegOracle.sol [Line 98](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L98)
```
          require(price1 > 0, "Chainlink price <= 0");
```
File: src\oracles\PegOracle.sol [Line 121](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L121)
```
          require(price2 > 0, "Chainlink price <= 0");
```
File: src\rewards\StakingRewards.sol [Line 119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L119)
```
          require(amount > 0, "Cannot withdraw 0");
```
File: src\rewards\StakingRewards.sol [Line 134](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L134)
```
if (reward > 0) {
```

### USE CUSTOM ERRORS INSTEAD OF REVERT STRINGS TO SAVE GAS
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met).
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

File: src\oracles\PegOracle.sol [Line 23](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L23)
```
        require(_oracle1 != address(0), "oracle1 cannot be the zero address");
```
File: src\oracles\PegOracle.sol [Line 24](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L24)
```
        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```
File: src\oracles\PegOracle.sol [Line 25](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L25)
```
        require(_oracle1 != _oracle2, "Cannot be same Oracle");
```
File: src\oracles\PegOracle.sol [Line 28-31](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L28-L31)
```
        require(
            (priceFeed1.decimals() == priceFeed2.decimals()),
            "Decimals must be the same"
        );
```
File: src\oracles\PegOracle.sol [Line 98](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L98)
```
          require(price1 > 0, "Chainlink price <= 0");
```
File: src\oracles\PegOracle.sol [Line 99-102](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L99-L102)
```
        require(
            answeredInRound1 >= roundID1,
            "RoundID from Oracle is outdated!"
        );
```
File: src\oracles\PegOracle.sol [Line 103](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L103)
```
        require(timeStamp1 != 0, "Timestamp == 0 !");
```
File: src\oracles\PegOracle.sol [Line 121](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L121)
```
        require(price2 > 0, "Chainlink price <= 0");
```
File: src\oracles\PegOracle.sol [Line 122-125](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L122-L125)
```
        require(
            answeredInRound2 >= roundID2,
            "RoundID from Oracle is outdated!"
        );
```
File: src\oracles\PegOracle.sol [Line 126](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L126)
```
        require(timeStamp2 != 0, "Timestamp == 0 !");
```
File: src\rewards\StakingRewards.sol [Line 217-220]()
```
        require(
            tokenAddress != address(stakingToken),
            "Cannot withdraw the staking token"
        );
```
File: src\rewards\StakingRewards.sol [Line 226-229]()
```
        require(
            block.timestamp > periodFinish,
            "Previous rewards period must be complete before changing the duration for the new period"
        );
```
