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

## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number. Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

## No Need to Initialize Variables with Default Values
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). If you explicitly initialize it with its default value, you will be incurring more gas. Hence, in the for loop of the following instances, refrain from doing i = 0. Here are some of the instances entailed:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

## ++i costs less gas compared to i++
++i costs less gas compared to i++ or i += 1 for unsigned integers considering the pre-increment operation is cheaper (about 5 GAS per iteration).

i++ increments i and makes the compiler create a temporary variable for returning the initial value of i. In contrast, ++i returns the actual incremented value without making the compiler do extra job.

As an example, the for loop below could be refactored as follows:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443-L450

```
        for (uint256 i ; i < epochsLength();) {
            if (epochs[i] == _epoch) {
                if (i == epochsLength() - 1) {
                    return 0;
                }
                return epochs[i + 1];        
            }
    
            unchecked {
                ++i;
            }
        }
```
Note: "Checked" math, which is default in 0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. Considering no arithmetic overflow/underflow is going to happen here, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas in the above for loop. 