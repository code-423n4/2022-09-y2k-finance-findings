Hello, team. 

Found strange variable initializations: 

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L59
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L65
...

Here type of price1 and price2 variables is int256. So, values of price1 and price2 may be = 0 or < 0. This can result in unexpected behaviour of contract. 
For example, at this string: 
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L68
nowPrice = (price2 * 10000) / price1;
if price1 = 0, contract would crash. So, here we may check if price1 > 0. And only in this case go further.

So, I think, that all price variables should initialize as uint type. And also non zero checks before division needed. 


