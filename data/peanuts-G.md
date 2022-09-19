## TABLE OF CONTENTS

- [G-01] ++I costs less gas as compared to I++ or I+= 1
- [G-02] Increments can be unchecked
- [G-03] X += Y cost more gas than X = X + Y for state variables
- [G-04] Using private rather than public for constants saves gas
- [G-05] Increments in for loop can be unchecked (saves 30-40 gas per loop iteration)
- [G-06] Cache Storage values in memory to minimize sloads


## [G-01] ++I costs less gas as compared to I++ or I+= 1

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled. 

File: Vault.sol [Line 443](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443)

```
for (uint256 i = 0; i < epochsLength(); i++) {
            if (epochs[i] == _epoch) {
                if (i == epochsLength() - 1) {
                    return 0;
                }
                return epochs[i + 1];
            }
        }
```
can become 
```
for (uint256 i = 0; i < epochsLength(); ++i) {
            if (epochs[i] == _epoch) {
                if (i == epochsLength() - 1) {
                    return 0;
                }
                return epochs[i + 1];
            }
        }
```

## [G-02] No need to explicitly initialize variables with default value

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas. In the similar code above, the initialization of uint256 i = 0 can be simplified to uint256 i

## [G-03] X += Y cost more gas than X = X + Y for state variables

File: VaultFactory.sol [Line 195](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L195)

```
marketIndex += 1;
```

becomes

```
marketIndex = marketIndex + 1;
```

## [G-04]

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

File: Controller.sol [Line 16](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L16)

```
uint256 public constant VAULTS_LENGTH = 2;
```

## [G-05] Increments in for loop can be unchecked (saves 30-40 gas per loop iteration)

The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop 

## [G-06] Cache Storage values in memory to minimize sloads

The code can be optimized by minimising the number of SLOADs. SLOADs are expensive 100 gas compared to MLOADs/MSTOREs(3gas)
Storage value should get cached in memory... eg. marketIndex

[Line 219](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L219)