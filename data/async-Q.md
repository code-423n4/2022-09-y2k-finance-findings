General notes. The variables `insr` and `hedge` are used interchangably in the codebase. Consider sticking with `hedge` to reduce confusion.

# VaultFactory controller address not validated
## Impact
The `controller` addresses can be different in the `VaultFactory` and `insr/risk` vaults.

Judge note: this could be intended functionality, but wanted to include the finding if that was not the case.

## Proof of Concept
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L295-L300
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L345-L359

In VaultFactory there are two functions to change the `controller` address - `setController()`, which sets `VaultFactory.controller`, and `changeController()`, which sets the vaults `insr.controller` and `risk.controller` address. 

Since `setController()` does not validate if the `controller` address is non-zero (and disallow setting the address if so) and `changeController()` accepts any arbitrary non-zero address this can lead to a scenario where the `VaultFactory.controller` address is different from the `insr.controller` and `risk.controller` address. 

Additionally, `changeController()` does not reset the VaultFactory's state variable `controller` address. So if a new market is created for a token after `changeController()` has been called then the expected `controller` address may not be what the market creator expects.

## Tools Used
Manual anaylsis

## Recommended Mitigation Steps

Refactor `changeController()` to use the `VaultFactory.controller` address instead of accepting an arbitrary address. Disallow setting a new `controller` in `setController()` when `controller != 0`, or remove this function entirely and set the `controller` address in the constructor.


# Use of assert() instead of require()
## Impact
In `Vault.depositETH` an `assert()` is used instead of a `require()` which causes a Panic error on failure and prevents the use of error strings.

0xRajeev explains it very well in their realitycards finding https://github.com/code-423n4/2021-06-realitycards-findings/issues/83

"Prior to solc 0.8.0, assert() used the invalid opcode which used up all the remaining gas while require() used the revert opcode which refunded the gas and therefore the importance of using require() instead of assert() was greater. However, after 0.8.0, assert() uses revert opcode just like require() but creates a Panic(uint256) error instead of Error(string) created by require(). Nevertheless, Solidity’s documentation says:

"Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix. Language analysis tools can evaluate your contract to identify the conditions and function calls which will cause a Panic.”

whereas

“The require function either creates an error without any data or an error of type Error(string). It should be used to ensure valid conditions that cannot be detected until execution time. This includes conditions on inputs or return values from calls to external contracts.”

Also, you can optionally provide a message string for require, but not for assert."

## Proof of Concept

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L182-L193

## Tools Used
Manual Analysis

## Recommended Mitigation Steps
Use require() with informative error strings instead of assert().