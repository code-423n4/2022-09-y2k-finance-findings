First Gas optimization - ">=" is cheaper than ">"

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol
187: require(msg.value > 0, "ZeroValue");
311: if(_withdrawalFee > 150)

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol
119: require(amount > 0, "Cannot withdraw 0");
134: if (reward > 0) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol
98: require(price1 > 0, "Chainlink price <= 0");
121: require(price2 > 0, "Chainlink price <= 0");

Second Gas optimization - Using unchecked blocks can save gas.

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol
88: if(block.timestamp > idEpochBegin[id] - timewindow)
227: entitledShares = entitledShares - feeValue;
445: if (i == epochsLength() - 1) {
448: return epochs[i + 1];

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol
195: marketIndex += 1;
