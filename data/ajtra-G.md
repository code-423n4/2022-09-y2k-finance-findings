# Index
[G01] Post-increment/decrement cost more gas then pre-increment/decrement
[G02] != 0 is cheaper than > 0 especially in state variables
[G03] I = I + (-) X is cheaper in gas cost than I += (-=) X
[G04] Require / Revert strings longer than 32 bytes cost extra gas
[G05] Use custom errors rather than REVERT()/REQUIRE() strings to save deployment gas
[G06] Using private rather than public for constants
[G07] Don't compare boolean expressions to boolean literals
[G08] Using bools for storage incurs overhead
[G09] ABI.ENCODE() is less efficient than ABI.ENCODEPACKED()
[G10] Using storage instead of memory for structs/arrays
[G11] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate
[G12] State variables only set in the constructor should be declared immutable
[G13] Should use unchecked in arithmetic operations when it's not possible to overflow
[G14] Initialize variables with default values are not needed
[G15] Not use local variable when it's going to be used once
[G16] Reordering code in Controller.getLatestPrice to save gas
[G17] Optimize getNextEpoch to save gas

# Details
## [G01] Post-increment/decrement cost more gas then pre-increment/decrement
### Description
++I (--I) cost less gas than I++ (I--) especially in a loop.

### Proof of concept

```solidity
contract TestPost {
	function testPost() public {
		uint256 i;
		i++;
	}
}
contract TestPre {
	function testPre() public {
		uint256 i;
		++i;
	}
}
```

- Transaction cost of testPost is 21333 gas
- Transaction cost of testPre is 21328 gas 
- After the test it's possible to save **5 gas per ocurrence** with this optimization.

### Lines in the code
[Vault.sol#L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443)


## [G02] != 0 is cheaper than > 0 especially in state variables
### Description
Replace all > 0 for != 0. In cases where we used a variable from state assigned to a local variable it's the same gas save.

### Proof of concept
```solidity
contract TestA {
    uint256 _paramA;

    function Test () public returns (bool) {
        return _paramA > 0;
    }

}

contract TestB {
    uint256 _paramA;

    function Test () public returns (bool) {
        return _paramA != 0;
    }

}

contract TestC {
    uint256 _paramA;

    function Test () public returns (bool) {
        uint256 aux = _paramA;
        return aux > 0;
    }

}

contract TestD {
    uint256 _paramA;

    function Test () public returns (bool) {
        uint256 aux = _paramA;
        return aux != 0;
    }

}
```

- Transaction cost of TestA/C is 23491/23504 gas
- Transaction cost of TestB/D is 23494/23507 gas 
- After the test it's possible to save **3 gas per ocurrence** with this optimization.

### Lines in the code
[Vault.sol#L311](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L311)
[StakingRewards.sol#L134](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L134)

## [G03] I = I + (-) X is cheaper in gas cost than I += (-=) X
This is especially with state variables. In the following example I'm trying to demostrate 
the save gas in a loop of 10 iterations.

### Proof of concept
```diff
contract TestA {
    uint256 i;
    function Test () public {
        
        for(;i<10;){
            i += 1;
        }
    }

}

contract TestB {
    uint256 i;
    function Test () public {
        
        for(;i<10;){
            i = i + 1;
        }
    }
}
```
- Transaction cost of TestA is 48793 gas
- Transaction cost of TestB is 48663 gas
- After the test it's possible to save **130 gas (13 gas per iteration/ocurrence)** with this optimization.

### Lines in the code
[VaultFactory.sol#L195](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L195)

## [G04] Require / Revert strings longer than 32 bytes cost extra gas
### Description
Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

### Lines in the code
[PegOracle.sol#L23](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L23)
[PegOracle.sol#L24](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L24)
[SemiFungibleVault.sol#L116](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L116)
[StakingRewards.sol#L217](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L217)
[StakingRewards.sol#L226](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L226)

## [G05] Use custom errors rather than REVERT()/REQUIRE() strings to save deployment gas
### Description
Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. 
Not defining the strings also save deployment gas.

### Lines in the code
[PegOracle.sol#L23](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L23)
[PegOracle.sol#L24](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L24)
[PegOracle.sol#L25](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L25)
[PegOracle.sol#L28](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L28)
[PegOracle.sol#L98](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L98)
[PegOracle.sol#L99](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L99)
[PegOracle.sol#L103](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L103)
[PegOracle.sol#L121](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L121)
[PegOracle.sol#L122](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L122)
[PegOracle.sol#L126](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L126)
[SemiFungibleVault.sol#L91](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L91)
[SemiFungibleVault.sol#L116](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L116)
[Vault.sol#L165](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L165)
[Vault.sol#L187](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L187)
[StakingRewards.sol#L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L96)
[StakingRewards.sol#L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L119)
[StakingRewards.sol#L202](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L202)
[StakingRewards.sol#L217](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L217)
[StakingRewards.sol#L226](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L226)

## [G06] Using private rather than public for constants

### Description
If needed, the value can be read from the verified contract source code. 
Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, 
and not adding another entry to the method ID table.

### Lines in the code
[Controller.sol#L16](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L16)

## [G07] Don't compare boolean expressions to boolean literals
### Description
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

### Proof of concept
```diff
contract TestA {
    function Test (bool paramA) public {
        require(paramA == true, "Test bool");
    }
}

contract TestB {
    function Test (bool paramA) public {
        require(paramA, "Test bool");
    }
}
```
- Transaction cost of TestA is 21629 gas
- Transaction cost of TestB is 21611 gas
- After the test it's possible to save **18 gas per ocurrence** with this optimization.

### Lines in the code
[Controller.sol#L93](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L93)
[Controller.sol#L211](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L211)
[Vault.sol#L80](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L80)
[Vault.sol#L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L96)
[Vault.sol#L217](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L217)
[Vault.sol#L314](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L314)
[RewardsFactory.sol#L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L96)

## [G08] Using bools for storage incurs overhead
### Description
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) 
when changing from 'false' to 'true', after having been 'true' in the past

### Lines in the code
[Vault.sol#L52](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L52)
[Vault.sol#L54](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L54)

## [G09] ABI.ENCODE() is less efficient than ABI.ENCODEPACKED()

### Lines in the code
[RewardsFactory.sol#L118](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L118)
[RewardsFactory.sol#L150](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L150)

## [G10] Using storage instead of memory for structs/arrays
### Description
When retrieving data from a memory location, assigning the data to a memory variable causes all fields of the struct/array to be read from memory, 
resulting in a Gcoldsload (2100 gas) for each field of the struct/array. 
When reading fields from new memory variables, they cause an extra MLOAD instead of a cheap stack read. 
Rather than declaring a variable with the memory keyword, it is much cheaper to declare a variable with the storage keyword and cache all fields 
that need to be read again in a stack variable, because the fields actually read will only result in a Gcoldsload. 
The only case where the entire struct/array is read into a memory variable is when the entire struct/array is returned by a function, 
passed to a function that needs memory, or when the array/struct is read from another store array/struc.

### Lines in the code
[VaultFactory.sol#L313](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L313)
[VaultFactory.sol#L331](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L331)
[VaultFactory.sol#L352](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L352)

## [G11] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate
### Description
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. 
Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash 
(Gkeccak256 - 30 gas) and that calculation's associated stack operations.

In the case of the variables StakingRewards.userRewardPerTokenPaid and StakingRewards._balances could save gas combining it in a struct.

### Lines in the code
[StakingRewards.sol#L43](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L43)
[StakingRewards.sol#L47](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L47)
	
## [G12] State variables only set in the constructor should be declared immutable
### Description
Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) 
and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

### Lines in the code
[RewardsFactory.sol#L9](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L9)
[RewardsFactory.sol#L10](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L10)
[RewardsFactory.sol#L11](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L11)
[VaultFactory.sol#L12](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L12)
[VaultFactory.sol#L13](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L13)
[SemiFungibleVault.sol#L19](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L19)
[SemiFungibleVault.sol#L20](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L20)
[SemiFungibleVault.sol#L21](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L21)
[PegOracle.sol#L10](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L10)
[PegOracle.sol#L11](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L11)
[PegOracle.sol#L13](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L13)

## [G13] Should use unchecked in arithmetic operations when it's not possible to overflow
### Description
This situation ocurrs especially in loops

Example,

```diff
- for (uint256 i = 0; i < epochsLength(); i++) {
+ for (uint256 i = 0; i < epochsLength();) {
     if (epochs[i] == _epoch) {
         if (i == epochsLength() - 1) {
             return 0;
         }
         return epochs[i + 1];
     }
+	 unchecked {
+		++i;
+	 }
  }
```

### Lines in the code
[Vault.sol#L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443)

## [G14] Initialize variables with default values are not needed
### Description
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address,...). 
Explicitly initializing it with its default value is an anti-pattern and wastes gas.

### Lines in the code
[Vault.sol#L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443)
[VaultFactory.sol#L159](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L159)
[StakingRewards.sol#L36](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L36)


## [G15] Not use local variable when it's going to be used once
### Description
Not store a value in a local variable when it's going to be used once, instead of that, use directly the value.

### VaultFactory.createNewMarket

```diff
-	MarketVault memory marketVault = MarketVault(marketIndex, epochBegin, epochEnd, hedge, risk, _withdrawalFee);

-   _createEpoch(marketVault);
+   _createEpoch(MarketVault(marketIndex, epochBegin, epochEnd, hedge, risk, _withdrawalFee));
```
[VaultFactory.sol#L234-L236](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L234-L236)

### VaultFactory.deployMoreAssets

```diff
-   address hedge = indexVaults[index][0];
-   address risk = indexVaults[index][1];

-   MarketVault memory marketVault = MarketVault(index, epochBegin, epochEnd, Vault(hedge), Vault(risk), _withdrawalFee);
+   MarketVault memory marketVault = MarketVault(index, epochBegin, epochEnd, Vault(indexVaults[index][0]), Vault(indexVaults[index][1]), _withdrawalFee);
```
[VaultFactory.sol#L260-L263](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L260-L263)

### VaultFactory.changeTreasury (same change in changeTimewindow and changeController)

```diff
-   Vault insr = Vault(vaults[0]);
-   Vault risk = Vault(vaults[1]);
-   insr.changeTreasury(_treasury);
-   risk.changeTreasury(_treasury);
+   Vault(vaults[0]).changeTreasury(_treasury);
+   Vault(vaults[1]).changeTreasury(_treasury);
```
[VaultFactory.sol#L314-L317](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L314-L317)
[VaultFactory.sol#L332-L335](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L332-L335)
[VaultFactory.sol#L353-L356](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L353-L356)

### Controller.getLatestPrice
```diff
-   bool isSequencerUp = answer == 0;

-   if (!isSequencerUp) {
+   if (!(answer == 0)) {
        revert SequencerDown();
    }
```
[Controller.sol#L277-L278](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L277-L278)

```diff
-   uint256 timeSinceUp = block.timestamp - startedAt;
-   if (timeSinceUp <= GRACE_PERIOD_TIME) {
+	if ((block.timestamp - startedAt) <= GRACE_PERIOD_TIME) {
         revert GracePeriodNotOver();
    }
```
[Controller.sol#L283-L286](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L283-L286)

```diff
-   uint256 decimals = 10**(18-(priceFeed.decimals()));
-   price = price * int256(decimals);
+   price = price * int256(10**(18-(priceFeed.decimals())));
```
[Controller.sol#L299-L300](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L299-L300)


## [G16] Reordering code in Controller.getLatestPrice to save gas

### Description
In the following part of the code of getLatestPrice it's possible to move the price calculation after
the two if's (if(answeredInRound < roundID) and if(timeStamp == 0)) due to those if's not depend on the price.
With this change it's possible to save gas in the situations when those if's are true.

```diff
-        uint256 decimals = 10**(18-(priceFeed.decimals()));
-        price = price * int256(decimals);

-        if(price <= 0)
-            revert OraclePriceZero();

        if(answeredInRound < roundID)
            revert RoundIDOutdated();

        if(timeStamp == 0)
            revert TimestampZero();

+        uint256 decimals = 10**(18-(priceFeed.decimals()));
+        price = price * int256(decimals);

+        if(price <= 0)
+            revert OraclePriceZero();

        return price;
```

### Lines in the code
[Controller.sol#L299-L311](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L299-L311)

## [G17] Optimize getNextEpoch to save gas
### Description
Store epochsLength() in a local variable due to it's wasting gas in every loop.
Would be a good option to use directly to epochs.length insted of call epochsLength() to avoid JUMP and save gas too.

```diff
+	uint256 length = epochsLength();
-   for (uint256 i = 0; i < epochsLength(); i++) {
+   for (uint256 i = 0; i < length; i++) {
    if (epochs[i] == _epoch) {
-   	if (i == epochsLength() - 1) {
+       if (i == length - 1) {
			return 0;
        }
        return epochs[i + 1];
      }
    }
```
### Lines in the code
[Vault.sol#L438-L451](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L438-L451)
