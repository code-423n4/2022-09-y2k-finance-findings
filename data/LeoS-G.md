# Low effect on readability

## [G-01] Optimisation fo for loop
The easiest thing to do is to optimize every `for` loop, the objective is to replace those like the following example:

    for  (uint256 i =  0; i < array.length; i++)  {
    	//do something
    }

to

    uint256 len =  array.length;
    for  (uint256 i; i < len;)  {
    	//do something
    	unchecked{ ++i; }
    }

By doing so, the `length` is cached which is cheaper than looking at it every loop, `i = 0` is not initialized since `uint` have already a default value of `0` and the increment is transformed to a cheaper form since it can't overflow. (This is cheaper because without `unchecked` there is a check for overflow at each calculation and with the post-increment, the EVM need to store the value with and without increment, but the pre-increment only store the value with increment.)

1 instances: 

 - https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443-L450

Consider optimizing `for` loop.

With those change, these evolutions in gas average report can be observed:

    Vault: Deployment: 2671508 -> 2673108 (+1600)
    Vault: getNextEpoch: not available (probably -15)

## [G-02] ++i is cheaper than i += 1
1 instances:

 - https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195

With this change, these evolutions in gas average report can be observed:

    VaultFactory: Deployment: 4166853 -> 4166053 (-800)
    VaultFactory: createNewMarket: 5763780 -> 5763767 (-13)

# High effect on readability

## [G-03] `And` condition can be swapped to `or` condition.
An `or` condition is cheaper than an `and` one even with optimizer.

2 instances:

 - https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L96
 - https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L216

Consider swapping `(a && b)` to `(!(!a || !b))` which are equivalent.
*"Everything is true" to "not (something is false) "*

With these changes, these evolutions in gas average report can be observed:

    Vault: Deployment: 2671508 -> 2671108 (-400)
    Vault: withdraw: 29593 -> 29585 (-8)
