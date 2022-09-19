## > 0 is less efficient than != 0 for unsigned integers 
!= 0 costs less gas compared to > 0 
for unsigned integers in require statements with the optimizer enabled (6 gas)

Proof: While it may seem that > 0 is cheaper than !=, 
this is only true without the optimizer enabled and outside a require statement. 
If you enable the optimizer at 10k AND you’re in a require statement, 
this will save gas. You can see this tweet for more proofs:
https://twitter.com/gzeon/status/1485428085885640706
I suggest changing > 0 with != 0 here:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119

## Loops can be optimized in several ways
To optimize the for loop and make it consume less gas, i suggest to:

1. If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas. 

2. Use ++i instead of i++, which is a cheaper operation (in this case there is no difference between i++ and ++i because we dont use the return value of this expression, which is the only difference between these two expression).

As an example: 
``` 
for (uint256 i = 0; i < Variable; i++) { 
```
should be replaced with 
```
for (uint256 i; i < Variable; ++i) {
```
I suggest to change the code here:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443