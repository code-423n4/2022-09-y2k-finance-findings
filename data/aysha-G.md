Title: Boolean Comparisons
Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(directValue) instead of if(directValue == true) here (same for require statements):
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L80
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L314
========================================================================
Title: It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied
Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443
