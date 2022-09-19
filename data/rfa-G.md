GAS

## [G-1] Using ! operator to validate var == false

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L96
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L217
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L211
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L80

By using ! operator instead of == false, we can save 11 gas per call

RECOMMEDED MITIGATION STEP
```solidity
    if((block.timestamp < id) && !idDepegged[id])
```


## [G-2] Using prefix increment instead += operator

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195

Using prefix increment than += can save 4 execution gas fee. And by using unchecked, we can save more 78 gas (markedIndex is almost impossible to go overflow)

```solidity
    unchecked{++marketIndex}
```
