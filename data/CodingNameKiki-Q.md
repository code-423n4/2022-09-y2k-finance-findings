1. Assert Violation

The Solidity assert() function is meant to assert invariants. Properly functioning code should never reach a failing assert statement. A reachable assertion can mean one of two things:

1.A bug exists in the contract that allows it to enter an invalid state;
2.The assert statement is used incorrectly, e.g. to validate inputs.

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L190

Recommended:
Consider whether the condition checked in the assert() is actually an invariant. If not, replace the assert() statement with a require() statement.If the exception is indeed caused by unexpected behaviour of the code, fix the underlying bug(s) that allow the assertion to be violated.

2. Missing checks for address(0x0) when assigning values to address state variables

Zero-address checks are a best practice for input validation of critical address parameters. While the codebase applies this to most cases, there are many places where this is missing in constructors and setters.
Impact: Accidental use of zero-addresses may result in exceptions, burn fees/tokens, or force redeployment of contracts.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L85-L101
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L110-L128
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L136
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L152-L174
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L182-L193
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L308-L320
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L68-L70

