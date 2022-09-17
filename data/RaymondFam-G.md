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