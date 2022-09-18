In Vault.sol contract:

For loop could be optimized by not initializing i variable (since its already 0 when declaring it), 
secondly not calling epochsLength() in each iteration, but rather initialize a new variable before the loop and call it in each iteration,
thirdly use ++i instead of i++ as it costs less gas.

190: In case the condition is not satisfied, assert will still consume all the gas, so it should be avoided. rather use require or revert here.
