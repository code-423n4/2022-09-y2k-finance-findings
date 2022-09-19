## [NAZ-G1] Unused Variables
**Context**: [`PegOracle.sol#L13`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L13)

**Description**:
The linked variables are unused and can be removed to save gas and improve readability.

**Recommendation**: 
Consider removing the unused variables.


## [NAZ-G] State Variables That Can Be Set To `Immutable`
**Context**: [`Controller.sol#L13`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L13), [`PegOracle.sol#L10-L13`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L10-L13), [`SemifungibleVault.sol#L20-L21`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L20-L21)

**Description**:
Solidity `0.6.5` introduced `immutable` as a major feature. It allows setting contract-level variables at construction time which gets stored in code rather than storage. Each call to it reads from storage, using a `sload` costing 2100 gas cold or 100 gas warm. Setting it to `immutable` will have each storage read of the state variable to be replaced by the instruction `push32 value`, where `value` is set during contract construction time and this costs only 3 gas.

**Recommendation**: 
Set the state variable to `immutable`.


## [NAZ-G2] Use of `2**256 - 1 && type(uint256).max` When `2**255` Can Be Used
**Context**: [`SemiFungibleVault.sol#L238`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L238), [`SemiFungibleVault.sol#L245`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L245)

**Description**:
Infinity can also be represented via ``2**255`, it's hex representation is `0x8000000000000000000000000000000000000000000000000000000000000000` while `2**256 - 1` is `0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff`. Then main difference is and where the gas savings come from is, zeros are cheaper than non-zero values in hex representation.

**Recommendation**: 
Use `2**255` instead of `2**256 - 1` to save gas on deployment.


## [NAZ-G3] Use `delete` Keyword To Reset State Data
**Context**: [`StakingRewards.sol#L135`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L135)

**Description**:
It's cheaper to use the `delete` keyword when resetting state data to their default values than setting them.

**Recommendation**: 
Consider using `delete` keyword to reset state data to default values.


## [NAZ-G4] In `require()`, Use `!= 0` Instead of `> 0` With Uint Values
**Context**: [`Vault.sol#L187`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187), [`StakingRewards.sol#L119`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119)

**Description**:
In a require, when checking a uint, using `!= 0` instead of `> 0` saves 6 gas. This will jump over or avoid an extra `ISZERO` opcode.

**Recommendation**: 
Use `!= 0` instead of `> 0` with uint values but only in `require()` statements.


## [NAZ-G5] Use of Custom Errors Instead of String
**Context**: [`PegOracle.sol`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol), [`SemiFungibleVault.sol`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol), [`Vault.sol#L165`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L165), [`Vault.sol#L187`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187), [`StakingRewards.sol`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol)

**Description**:
To save some gas the use of custom errors leads to cheaper deploy time cost and run time cost. The run time cost is only relevant when the revert condition is met.

**Recommendation**: 
Use Custom Errors instead of strings.


## [NAZ-G6] Skip Initialization of Variable
**Context**: [`StakingRewards.sol#L36`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L36)

**Description**:
It's cheaper to skip initializtion of state variables to their default values.

**Recommendation**:
Consider skipping the initialization of these listed varaible.


## [NAZ-G7] Use `++index` instead of `index++` to increment a loop counter
**Context**: [`Vault.sol#L443`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443)

**Description**:
Due to reduced stack operations, using `++index` saves 5 gas per iteration.

**Recommendation**: 
Use `++index `to increment a loop counter.


## [NAZ-G8] The Increment In For Loop Post Condition Can Be Made Unchecked
**Context**: [`Vault.sol#L443`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443)

**Description**:
(This is only relevant if you are using the default solidity checked arithmetic). `i++` involves checked arithmetic, which is not required. This is because the value of `i` is always strictly less than length <= 2**256 - 1. Therefore, the theoretical maximum value of `i` to enter the for-loop body is `2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. One can manually do this by:
```js
for (uint i = 0; i < length; ) {
    // do something that doesn't change the value of i
    unchecked {
        ++i;
    }
}
```

**Recommendation**: 
Consider doing the increment in the for loop post condition in an unchecked block.


## [NAZ-G9] Setting The Constructor To Payable
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src)

**Description**:
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of `msg.value == 0` and saves 21 gas on deployment with no security risks.

**Recommendation**: 
Set the constructor to payable.


## [NAZ-G10] Function Ordering via Method ID
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src)

**Description**:
Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. One could use [`This tool`](https://emn178.github.io/solidity-optimize-name/) to help find alternative function names with lower Method IDs while keeping the original name intact.

**Recommendation**: 
Find a lower method ID name for the most called functions for example ```mostCalled()``` vs. ```mostCalled_41q()``` is cheaper by 44 gas.