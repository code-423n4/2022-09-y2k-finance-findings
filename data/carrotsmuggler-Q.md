# Low

## 1. Check epochBegin time when creating a new vault 

When creating a new vault, epochBegin time should be checked against the block.timestamp. Otherwise can lead to the creation of unusable vaults.
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L307-L325

## 2. Withdrawal before epoch begins

Maybe allow the withdrawal of funds before the epoch begins to fix mistakes etc regarding deposit amounts. Can be ignored if this is the intended method of operation.

# Non-Critical

## 1. Inconsistent usage of vault names

Some places address the vault as insr, some places address it as hedge. Advised to have consistent naming to prevent confusion.

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L314
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L199

## 2. Wrong maths in comment
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L265

Incorrect expression in comments. Should be 
// 0.5% = multiply by 5 then divide by 1000

## 3. Failing test in  AssertTest.t.sol:testOwnerAuthorize

The test in 

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/test/AssertTest.t.sol#L493-L528

fails since the controller is never called to end the epoch. Insert the line `controller.triggerEndEpoch(1, endEpoch);` before starting the withdrawal process.

## 4. Failing fuzz tests in FuzzTest.t.sol

2 Fuzz tests fail in FuzzTest.t.sol, both due to the same reason: an issue in the test function testFuzzDeposit().

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/test/FuzzTest.t.sol#L17-L61

The reason for failure is that the FixedPointMathLib.sol library can only handle uint128 numbers. It multiplies two numbers and stores them in z

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/lib/solmate/src/utils/FixedPointMathLib.sol#L41

This only works if the product fits in uint256, which can be guaranteed with uint128 numbers. The fuzz test sends a number larger than uint128 since it expects to test uint256, making the test revert. A simple fix is to put in an assume statement at the top, only accepting uint96. uint96 as an example, because we want the result of the number * MULTIPLIER to fit within a uint128. Add the assume as below

`vm.assume(ethValue < type(uint96).max && ethValue > 1);`

The second condition is needed otherwise it reverts if sent 0 ETH by design.
The other failing fuzz test fails because it calls this function. So adding this one line will fix all tests in FuzzTest.t.sol.




