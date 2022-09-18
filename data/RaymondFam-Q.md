## Un-indexed Parameters in Events
Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute indexed which will cause the respective arguments to be treated as log topics instead of data. The following links are some of the instances entailed:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L49-L56

## Unused State Variable
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L13

The state variable `decimals` has been assigned `priceFeed1.decimals()` in the constructor, but it is never used in the contract. It could have been used in the following line of code such that:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L73

```
        int256 decimals10 = int256(10**(18 - decimals);
```

