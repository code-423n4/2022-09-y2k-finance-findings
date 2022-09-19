
## 1. Pre-increment costs less gas as compared to Post-increment :

++i costs less gas as compared to i++ for unsigned integer, as per-increment is cheaper(its about 5 gas per iteration cheaper)

i++ increments i and returns initial value of i. Which means

```
uint i =  1;
i++; // ==1 but i ==2
```

But ++i returns the actual incremented value:

```
uint i = 1;
++i; // ==2 and i ==2 , no need for temporary variable here
```

In the first case, the compiler has create a temporary variable (when used) for returning 1 instead of 2.

### Instances:
[Vault.sol:L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443)
[VaultFactory.sol:L195](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195)
```
src/Vault.sol:443:        for (uint256 i = 0; i < epochsLength(); i++) {
src/VaultFactory.sol:195:        marketIndex += 1;
``` 

### Recommendations:
Change post-increment to pre-increment.

-----

## 2. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

### Instances:
[Vault.sol:L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443)
```
src/Vault.sol:443:        for (uint256 i = 0; i < epochsLength(); i++) {
``` 

### Reference:

[https://code4rena.com/reports/2022-05-cally#g-11-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops](https://code4rena.com/reports/2022-05-cally#g-11-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops)

-----

## 3. Using > 0 costs more gas than != 0 when used on a uint in a require() statement

> 0 is less efficient than != 0 for unsigned integers (with proof)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas) Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proof: https://twitter.com/gzeon/status/1485428085885640706

### Instances
[Vault.sol:L187](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187)
[PegOracle.sol:L98](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L98)
[PegOracle.sol:L121](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L121)
[StakingRewards.sol:L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119)
```
src/Vault.sol:187:        require(msg.value > 0, "ZeroValue");
src/oracles/PegOracle.sol:98:        require(price1 > 0, "Chainlink price <= 0");
src/oracles/PegOracle.sol:121:        require(price2 > 0, "Chainlink price <= 0");
src/rewards/StakingRewards.sol:119:        require(amount > 0, "Cannot withdraw 0");
``` 

### Reference:

[https://twitter.com/gzeon/status/1485428085885640706](https://twitter.com/gzeon/status/1485428085885640706)

### Remediation:

I suggest changing > 0 with != 0. Also, please enable the Optimizer.

-----


## 4. Custom Errors instead of Revert Strings to save Gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met). Starting from Solidity v0.8.4,there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");),but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.
Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

### Instances
[Vault.sol:L165](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L165)
[Vault.sol:L187](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187)
[PegOracle.sol:L23](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L23)
[PegOracle.sol:L24](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L24)
[PegOracle.sol:L25](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L25)
[PegOracle.sol:L98](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L98)
[PegOracle.sol:L103](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L103)
[PegOracle.sol:L121](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L121)
[PegOracle.sol:L126](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L126)
[SemiFungibleVault.sol:L91](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L91)
[StakingRewards.sol:L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L96)
[StakingRewards.sol:L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119)
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


### Remediation:
I suggest replacing revert strings with custom errors.

-----

## 5. Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded the remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas. (see [https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions](https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions)).
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).
From the current usage of assert, my guess is that they can be replaced with require unless a Panic really is intended.

### Instances:
[Vault.sol:L190](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190)
```
src/Vault.sol:190:        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
``` 

### References:

[https://code4rena.com/reports/2022-05-bunker/#g-08-usage-of-assert-instead-of-require](https://code4rena.com/reports/2022-05-bunker/#g-08-usage-of-assert-instead-of-require)

-----

## 6. 10 ** 18 can be changed to 1e18 and save some gas

### Instances:
[Controller.sol:L299](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L299)
[PegOracle.sol:L73](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L73)
```
src/Controller.sol:299:        uint256 decimals = 10**(18-(priceFeed.decimals()));
src/oracles/PegOracle.sol:73:        int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
``` 

### Recommendations:
10 ** 18 can be changed to 1e18 to avoid unnecessary arithmetic operation and save gas.

### References:

[https://github.com/code-423n4/2021-12-yetifinance-findings/issues/274](https://github.com/code-423n4/2021-12-yetifinance-findings/issues/274)

-----

## 7. Boolean Comparision

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here

### Instances:
[Vault.sol:L314](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L314)
[Controller.sol:L93](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L93)
[Controller.sol:L211](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L211)
[Vault.sol:L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L96)
[RewardsFactory.sol:L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L96)
```
src/Vault.sol:314:        if(idExists[epochEnd] == true)
src/Controller.sol:93:        if(vault.idExists(epochEnd) == false)
src/Controller.sol:211:        if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
src/Vault.sol:96:        if((block.timestamp < id) && idDepegged[id] == false)
src/rewards/RewardsFactory.sol:96:        if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
``` 
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-04-fundingonlywhenpricenotflagged-boolean-comparison-147](https://code4rena.com/reports/2022-04-badger-citadel/#g-04-fundingonlywhenpricenotflagged-boolean-comparison-147)


-----

## 8. Strict inequalities (>) are more expensive than non-strict ones (>=)

Strict inequalities (>) are more expensive than non-strict ones (>=). This is due to some supplementary checks (ISZERO, 3 gas. I suggest using >= instead of > to avoid some opcodes here:

### Instances:
[Controller.sol:L102](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L102)
[Controller.sol:L106](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L106)
[StakingRewards.sol:L227](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L227)
```
src/Controller.sol:102:            vault.idEpochBegin(epochEnd) > block.timestamp)
src/Controller.sol:106:            block.timestamp > epochEnd
src/rewards/StakingRewards.sol:227:            block.timestamp > periodFinish,
``` 
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than](https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than)


-----

## 9. x += y costs more gas than x = x + y for state variables

### Instances:
[VaultFactory.sol:L195](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195)
```
src/VaultFactory.sol:195:        marketIndex += 1;
``` 
### References:

[https://github.com/code-423n4/2022-05-backd-findings/issues/108](https://github.com/code-423n4/2022-05-backd-findings/issues/108)


-----

## 10. Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use `uint number;` instead of `uint number = 0;`

### Instances:
[StakingRewards.sol:L36](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L36)
```
src/rewards/StakingRewards.sol:36:    uint256 public periodFinish = 0;
``` 
### Recommendation:

I suggest removing explicit initializations for default values.

-----


## 11. abi.encode() is less efficient than abi.encodepacked()

Use abi.encodepacked() instead of abi.encode()

### Instances:
[RewardsFactory.sol:L118](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L118)
[RewardsFactory.sol:L150](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L150)
[RewardsFactory.sol:L118](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L118)
[RewardsFactory.sol:L150](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L150)
```
src/rewards/RewardsFactory.sol:118:        bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));
src/rewards/RewardsFactory.sol:150:        return keccak256(abi.encode(_index, _epoch));
src/rewards/RewardsFactory.sol:118:        bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));
src/rewards/RewardsFactory.sol:150:        return keccak256(abi.encode(_index, _epoch));
``` 
### Reference:

[https://code4rena.com/reports/2022-06-notional-coop#15-abiencode-is-less-efficient-than-abiencodepacked](https://code4rena.com/reports/2022-06-notional-coop#15-abiencode-is-less-efficient-than-abiencodepacked)

-----

## 12. Reduce the size of error messages (Long revert Strings)

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Instances:
[PegOracle.sol:L23](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L23)
[PegOracle.sol:L24](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L24)
```
src/oracles/PegOracle.sol:23:        require(_oracle1 != address(0), "oracle1 cannot be the zero address");
src/oracles/PegOracle.sol:24:        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
``` 
### Recommendations:

I suggest shortening the revert strings to fit in 32 bytes, or using custom errors as described next.

Custom errors from Solidity 0.8.4 are cheaper than revert strings
(cheaper deployment cost and runtime cost when the revert condition is
met)

Source [Custom Errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) in Solidity:


-----


## 13. Using bools for storage incurs overhead

```
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
```

[Refer Here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Instances:
[Controller.sol:L277](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L277)
[Vault.sol:L52](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L52)
[Vault.sol:L54](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L54)
```
src/Controller.sol:277:        bool isSequencerUp = answer == 0;
src/Vault.sol:52:    mapping(uint256 => bool) public idDepegged;
src/Vault.sol:54:    mapping(uint256 => bool) public idExists;
``` 
### References:

[https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead](https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead)


-----

