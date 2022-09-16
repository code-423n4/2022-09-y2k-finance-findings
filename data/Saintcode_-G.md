## USE CALLDATA INSTEAD OF MEMORY

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

When arguments are read-only on external functions, the data location should be `calldata`

1 Instance:
-VaultFactory.sol line 185, 

## USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another memory array/struct

3 Instances: 
-VaultFactory.sol lines 313, 331, 352

## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

1 Instance:
-VaultFactory.sol line 195

## `++I` INSTEAD OF `I++` (OR USE ASSEMBLY WHEN APPLICABLE)

Use `++i` instead of ` i++`. This is especially useful in for loops but this optimization can be used anywhere in your code. 

1 instance:
-Vault.sol line 443

## IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.
```

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {

```
2 Instances:
-Vault.sol line 443
-StakingRewards.sol line 36


## `>=` COSTS LESS GAS THAN `>`

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas

6 Instances:
-Vault.sol lines 187, 311
-PegOracle.sol lines 98, 121
-StakingRewards lines 119, 134, 

## `++I/I++` SHOULD BE `UNCHECKED{++I}`/`UNCHECKED{I++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
```
   for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
           // Do the thing
           // Unchecked pre-increment is cheapest
           unchecked { ++i; }   
}  
```


1 instance:
-Vault.sol line 443

## `REQUIRE()`/`REVERT()` STRINGS LONGER THAN 32 BYTES COST EXTRA GAS
Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

5 Instances:
-SemiFungibleVault.sol line 116
-PegOracle.sol lines 23, 24, 25
-StakingRewards.sol lines 219, 228


## DON’T USE SAFEMATH ONCE THE SOLIDITY VERSION IS 0.8.0 OR GREATER
Version 0.8.0 introduces internal overflow checks, so using `SafeMath` is redundant and adds overhead

-StakingRewards.sol line 29, therefore .add(), .sub(), mul() and .div() functions should be changed to normal solidity operators, lines: 97, 98, 164, 178, 194, 208, 120, 121, 166, 176, 192, 169, 177, 190, 194, 203, 152, 167, 168, 176, 193



## USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

19 Instances:
-SemiFungible.sol lines 91, 116
-Vault.sol lines 165, 187
-PegOracle.sol lines 23, 24, 25, 28, 98, 99, 103, 121, 122, 126
-StakingRewards.sol lines 96, 119, 202, 217, 226


