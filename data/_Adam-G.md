### [G01] Minimizing SLOAD's
**Description:**
Storage variables that are used more than once without being modified can be cached to save roughly ~97 gas per use. (Costs 100 Gas to store then 3 gas per reference vs 100 gas per reference)

**LOC:**
[RewardsFactory.sol#L99-L116](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L99-L116) - admin is used 4 times and can be cached to save ~288 gas
[StakingRewards.sol#L99-L106](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L99-L106) - id is used 2 times and can be cached to save ~94 gas
[StakingRewards.sol#L203-L208](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L203-L208) - rewardsDuration is used 2 times and can be cached to save ~94 gas
[Vault.sol#L189-L190](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L189-L190) - asset is used 2 times and can be cached to save ~94 gas
[VaultFactory.sol#L188-L217](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L188-L217) - controller is used 4 times and can be cached to save ~288 gas
[VaultFactory.sol#L199-L217](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L199-L217) - treasury is used 2 times and can be cached to save ~94 gas
[VaultFactory.sol#L225-L234](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L225-L234) - marketIndex is used 2 times and can be cached to save ~94 gas


### [G02] For Loop Optimisations

**Description:**
epochsLength() is called twice every iteration in getNextEpoch, roughly ~194 gas per iteration can be saved by caching the result to a local variable before the for loop and referencing that instead.

When incrementing i in for loops there is no chance of overflow so unchecked can be used to save gas. I ran a simple test in remix and found deployment savings of 31,653 gas and on each function call saved ~141 gas per iteration.

```
contract Test {
	function loopTest() external {
		for (uint256 i; i < 1; ++i) {
		Deployment Cost: 125,637, Cost on function call: 24,601
		vs
		for (uint256 i; i < 1; ) {
		// for loop body
		unchecked { ++i }
		Deployment Cost: 93,984, Cost on function call: 24,460
		}
	}
}
```

In for loops pre increments can also be used to save a small amount of gas per iteraition.
I ran a test in remix using a for loop and found the deployment savings of 497 gas and ~5 gas per iteration.

```
contract Test {
	function loopTest() external {
		for (uint256 i; i < 1; i++) {
		(Deployment cost: 118,408, Cost on function call: 24,532)
		vs
		for (uint256 i; i < 1; ++i) {
		(Deployment cost: 117,911, Cost on function call: 24,527)
		}
	}
}
```

**LOC:**
[Vault.sol#L443-L450](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443-L450) 

**Recommendation:**
Update the for loop to 
```
_epochsLength = epochsLength();
for (uint256 i = 0; i < _epochsLength;) {
	if (epochs[i] == _epoch) {
		if (i == _epochsLength - 1) {
			return 0;
		}
		return epochs[i + 1];
	}
	unchecked { ++i }
}
```


### [G03] Emitting Storage Variables
**Description:**
You can save reading from storage (~100 gas) by emiting local variables over storage variables when they have the same value.

**LOC:**
[StakingRewards.sol#L231](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L231)

**Recommendation:**
Change line 166 to: emit RewardsDurationUpdated(`_rewardsDuration`); 


### [G04] Initialising to Default Values
**Description:**
When initialising a variable to its default variable, it is cheaper to leave blank. I ran a test in remix that initialises a single variable and got a saving of 2,246 gas. 

```
contract Test {
    uint256 public variable = 0;     (69,312 gas)
    vs
    uint256 public variable;         (67,066 gas)
}
```

**LOC:**
[StakingRewards.sol#L36](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L36)

**Recommendation:**
Change line 36 to - uint256 public periodFinish;


### [G05] Assigning Storage Variables to the same Value
**Description:**
Variables that are being assigned their default value in the constructor do not need to be reinitialised as they will already have that value. Removing the line from the constructor will save ~2200 gas.

**LOC:**
[VaultFactory.sol#L17](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L17)
[VaultFactory.sol#L159](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L159)

**Recommendation:**
marketIndex already equals 0 and line 159 can be removed.


### [G06] Storage Variables that can be Immutable
**Description:**
Variables that are initialised in the constructor and then never modified and can be changed to immutable to save gas.

**LOC:**
[RewardsFactory.sol#L9-L11](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L9-L11) - admin, govToken & factory can be updated to immutable
[SemiFungibleVault.sol#L20-L21](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L20-L21) - name & symbol can be updated to immutable.
[PegOracle.sol#L10-L16](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L10-L16) - oracle1, oracle2, decimals, priceFeed1 & priceFeed2 can be updated to immutable
[StakingRewards.sol#L41](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L41) - id can be updated to immutable

**Recommendation:**
Update the listed variables to immutable.


### [G07] x = x + y is Cheaper than x += y
**Description:**
Based on test in remix you can save ~1,007 gas on deployment and ~15 gas on execution cost if you use x = x + y over x += y (Is only true for Storage Variables).

```
contract Test {
	uint256 x = 1;
	function test() external {
		x += 3; 
		(Deployment Cost: 153,124, Execution Cost: 30,369)
		vs
		x = x + 1;
		(Deployment Cost: 152,117, Execution Cost: 30,354)
	}

}
```

**LOC:**
[VaultFactory.sol#L195](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L195)

**Recommendation:**
Change Line 195 to - marketIndex = marketIndex + 1;


### [G08] Custom Errors
**Description:**
As your using a solidity version > 0.8.4 you can replace revert strings with custom errors. This will save in deployment costs and runtime costs.
Based on a test in remix, replacing a single revert string with a custom error saved 12,404 gas in deployment cost and 86 gas on each function call. 

```
contract Test {
	uint256 a;
	function check() external {
		require(a != 0, "check failed");
	}
}   (Deployment cost: 114,703, Cost on Function call: 23,392)
vs 
contract Test {
	uint256 a;
	error checkFailed();
	function check() external {
		if (a != 0) revert checkFailed();
	}
}   (Deployment cost: 102,299, Cost on Function call: 23,306)
```

**LOC:**
[PegOracle.sol#L23-L28](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L23-L28) 
[PegOracle.sol#L98-L103](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L98-L103) 
[PegOracle.sol#L121-L126](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L121-L126) 
[SemiFungibleVault.sol#L91](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L91)
[SemiFungibleVault.sol#L116-L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L116-L119)
[Vault.sol#L165](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L165)
[Vault.sol#L187](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L187)
[StakingRewards.sol#L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L96)
[StakingRewards.sol#L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L119)
[StakingRewards.sol#L202-L205](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L202-L205)
[StakingRewards.sol#L217-L220](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L217-L220)
[StakingRewards.sol#L226-L229](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L226-L229)

**Recommendation:**
Update revert strings to custom errors.


### [G09] Long Revert Strings
**Description:**
If opting not to update revert strings to custom errors, keeping revert strings <= 32 bytes in length will save gas. 
I ran a test in remix and found the savings for a single short revert string vs long string to be 9,377 gas in deployment cost and 18 gas on function call.

```
contract Test {
	uint256 a;
	function check() external {
		require(a != 0, "short error message"); 
		(Deployment cost: 114,799, Cost on function call: 23,392)	
		vs 
		require(a != 0, "A longer Error Message over 32 bytes in     
        length"); 
		(Deployment cost: 124,176, Cost on function call: 23,410)	
	}
}
```

**LOC:**
[PegOracle.sol#L23-L24](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L23-L24)
[SemiFungibleVault.sol#L116-L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L116-L119)
[StakingRewards.sol#L217-L220](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L217-L220)
[StakingRewards.sol#L226-L229](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L226-L229)

**Recommendation:**
Either update to custom errors or shorten revert strings to be less than 32 bytes in length.