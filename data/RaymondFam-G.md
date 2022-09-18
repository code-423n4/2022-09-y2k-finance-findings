## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (< + =.) Consider replacing <= with the strict counterpart <. For instance, the following lines of code could respectively be rewritten as:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L302

```
if(price < 1)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L284

```
if (timeSinceUp < GRACE_PERIOD_TIME + 1)
```
## Use Custom Errors Instead of Require to Save Gas
Consider replacing all require statements with custom errors which are cheaper both in deployment and runtime cost starting from Solidity 0.8.4. Here are some of the instances entailed:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L23-L25
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L28-L31
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L98-L103

## Assert Costs More Gas Than Require
The `assert()` function when false, uses up all the remaining gas and reverts all the changes made. On the other hand, a `require()` function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190

On a side note, the assert function should only be used to examine invariants and test for internal problems. When used correctly, it can assess your contract and discover the conditions and function calls that will result in a failed assert. A properly running program should never reach a failing assert statement; if this occurs, there is a flaw in your contract that has to be addressed.