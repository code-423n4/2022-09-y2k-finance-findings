# 2022-09-Y2K-FINANCE

## Gas Optimizations Report

### The usage of `++i` will cost less gas than `i++`. The same change can be applied to `i--` as well.

This change would save up to 6 gas per instance/loop.

_There are **1** instances of this issue:_

```solidity
File: src/Vault.sol

443:  for (uint256 i = 0; i < epochsLength(); i++) {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol

### State variables should be cached in stack variables rather than re-reading them.

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

_There are **6** instances of this issue:_

```solidity
File: src/rewards/RewardsFactory.sol

      /// @audit Cache `admin`. Used 4 times in `createStakingRewards`
100:  admin,
101:  admin,
109:  admin,
110:  admin,
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol

```solidity
File: src/rewards/StakingRewards.sol

      /// @audit Cache `rewardsDuration`. Used 4 times in `notifyRewardAmount`
190:  rewardRate = reward.div(rewardsDuration);
194:  rewardRate = reward.add(leftover).div(rewardsDuration);
203:  rewardRate <= balance.div(rewardsDuration),
208:  periodFinish = block.timestamp.add(rewardsDuration);
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol

```solidity
File: src/Vault.sol

      /// @audit Cache `idFinalTVL`. Used 3 times in `beforeWithdraw`
400:  amount.divWadDown(idFinalTVL[id]).mulDivDown(
407:  entitledAmount = amount.divWadDown(idFinalTVL[id]).mulDivDown(
419:  entitledAmount = amount.divWadDown(idFinalTVL[id]).mulDivDown(

      /// @audit Cache `idClaimTVL`. Used 3 times in `beforeWithdraw`
401:  idClaimTVL[id],
408:  idClaimTVL[id],
420:  idClaimTVL[id],
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol

```solidity
File: src/VaultFactory.sol

      /// @audit Cache `controller`. Used 3 times in `createNewMarket`
188:  IController(controller).getVaultFactory() != address(this)
206:  controller
216:  controller

      /// @audit Cache `marketIndex`. Used 3 times in `createNewMarket`
219:  indexVaults[marketIndex] = [address(hedge), address(risk)];
226:  marketIndex,
234:  MarketVault memory marketVault = MarketVault(marketIndex, epochBegin, epochEnd, hedge, risk, _withdrawalFee);
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol

### Using `!= 0` on `uints` costs less gas than `> 0`.

This change saves 3 gas per instance/loop

_There are **2** instances of this issue:_

```solidity
File: src/rewards/StakingRewards.sol

119:  require(amount > 0, "Cannot withdraw 0");

134:  if (reward > 0) {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol

### It costs more gas to initialize non-`constant`/non-`immutable` variables to zero than to let the default of zero be applied

Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings

_There are **2** instances of this issue:_

```solidity
File: src/rewards/StakingRewards.sol

36:   uint256 public periodFinish = 0;
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol

```solidity
File: src/Vault.sol

443:  for (uint256 i = 0; i < epochsLength(); i++) {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol

### Using `private` rather than `public` for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

_There are **1** instances of this issue:_

```solidity
File: src/Controller.sol

16:   uint256 public constant VAULTS_LENGTH = 2;
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol

### Functions that are access-restricted from most users may be marked as `payable`

Marking a function as `payable` reduces gas cost since the compiler does not have to check whether a payment was provided or not. This change will save around 21 gas per function call.

_There are **3** instances of this issue:_

```solidity
File: src/rewards/StakingRewards.sol

225:  function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol

```solidity
File: src/VaultFactory.sol

295:  function setController(address _controller) public onlyAdmin {

366:  function changeOracle(address _token, address _oracle) public onlyAdmin {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol

### `++i`/`i++` should be `unchecked{++I}`/`unchecked{I++}` in `for`-loops

When an increment or any arithmetic operation is not possible to overflow it should be placed in `unchecked{}` block. \This is because of the default compiler overflow and underflow safety checks since Solidity version 0.8.0. \In for-loops it saves around 30-40 gas **per loop**

_There are **1** instances of this issue:_

```solidity
File: src/Vault.sol

443:  for (uint256 i = 0; i < epochsLength(); i++) {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol

### `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

_There are **1** instances of this issue:_

```solidity
File: src/VaultFactory.sol

195:  marketIndex += 1;
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol

### Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead

'When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.' \ https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html \ Use a larger size then downcast where needed

_There are **1** instances of this issue:_

```solidity
File: src/oracles/PegOracle.sol

13:   uint8 public decimals;
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol

### Don't compare boolean expressions to boolean literals

Use `if(x)`/`if(!x)` instead of `if(x == true)`/`if(x == false)`.

_There are **6** instances of this issue:_

```solidity
File: src/Controller.sol

93:   if(vault.idExists(epochEnd) == false)

211:  if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol

```solidity
File: src/rewards/RewardsFactory.sol

96:   if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol

```solidity
File: src/Vault.sol

96:   if((block.timestamp < id) && idDepegged[id] == false)

217:  isApprovedForAll(owner, receiver) == false)

314:  if(idExists[epochEnd] == true)
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol

### Use `calldata` instead of `memory` for function parameters

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

_There are **2** instances of this issue:_

```solidity
File: src/VaultFactory.sol

185:  string memory _name

269:  MarketVault memory _marketVault
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas

_There are **7** instances of this issue:_

```solidity
File: src/Controller.sol

284:  if (timeSinceUp <= GRACE_PERIOD_TIME) {

302:  if(price <= 0)
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol

```solidity
File: src/oracles/PegOracle.sol

100:  answeredInRound1 >= roundID1,

123:  answeredInRound2 >= roundID2,
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol

```solidity
File: src/rewards/StakingRewards.sol

189:  if (block.timestamp >= periodFinish) {

203:  rewardRate <= balance.div(rewardsDuration),
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol

```solidity
File: src/Vault.sol

317:  if(epochBegin >= epochEnd)
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol

### Use `immutable` & `constant` for state variables that do not change their value

_There are **12** instances of this issue:_

```solidity
File: src/Controller.sol

13:   AggregatorV2V3Interface internal sequencerUptimeFeed;
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol

```solidity
File: src/oracles/PegOracle.sol

10:   address public oracle1;

11:   address public oracle2;

13:   uint8 public decimals;

15:   AggregatorV3Interface internal priceFeed1;

16:   AggregatorV3Interface internal priceFeed2;
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol

```solidity
File: src/rewards/RewardsFactory.sol

9:    address public admin;

10:   address public govToken;

11:   address public factory;
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol

```solidity
File: src/rewards/StakingRewards.sol

41:   uint256 public id;
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol

```solidity
File: src/SemiFungibleVault.sol

20:   string public name;

21:   string public symbol;
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol

### `require()/revert()` strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

_There are **5** instances of this issue:_

```solidity
File: src/oracles/PegOracle.sol

23:   require(_oracle1 != address(0), "oracle1 cannot be the zero address");

24:   require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol

```solidity
File: src/rewards/StakingRewards.sol

217:  require(
218:      tokenAddress != address(stakingToken),
219:      "Cannot withdraw the staking token"
220:  );

226:  require(
227:      block.timestamp > periodFinish,
228:      "Previous rewards period must be complete before changing the duration for the new period"
229:  );
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol

```solidity
File: src/SemiFungibleVault.sol

116:  require(
117:      msg.sender == owner || isApprovedForAll(owner, receiver),
118:      "Only owner can withdraw, or owner has approved receiver for all"
119:  );
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol

### Use custom errors rather than `revert()`/`require()` strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

_There are **19** instances of this issue:_

```solidity
File: src/oracles/PegOracle.sol

23:   require(_oracle1 != address(0), "oracle1 cannot be the zero address");

24:   require(_oracle2 != address(0), "oracle2 cannot be the zero address");

25:   require(_oracle1 != _oracle2, "Cannot be same Oracle");

28:   require(
29:       (priceFeed1.decimals() == priceFeed2.decimals()),
30:       "Decimals must be the same"
31:   );

98:   require(price1 > 0, "Chainlink price <= 0");

99:   require(
100:      answeredInRound1 >= roundID1,
101:      "RoundID from Oracle is outdated!"
102:  );

103:  require(timeStamp1 != 0, "Timestamp == 0 !");

121:  require(price2 > 0, "Chainlink price <= 0");

122:  require(
123:      answeredInRound2 >= roundID2,
124:      "RoundID from Oracle is outdated!"
125:  );

126:  require(timeStamp2 != 0, "Timestamp == 0 !");
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol

```solidity
File: src/rewards/StakingRewards.sol

96:   require(amount != 0, "Cannot stake 0");

119:  require(amount > 0, "Cannot withdraw 0");

202:  require(
203:      rewardRate <= balance.div(rewardsDuration),
204:      "Provided reward too high"
205:  );

217:  require(
218:      tokenAddress != address(stakingToken),
219:      "Cannot withdraw the staking token"
220:  );

226:  require(
227:      block.timestamp > periodFinish,
228:      "Previous rewards period must be complete before changing the duration for the new period"
229:  );
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol

```solidity
File: src/SemiFungibleVault.sol

91:   require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");

116:  require(
117:      msg.sender == owner || isApprovedForAll(owner, receiver),
118:      "Only owner can withdraw, or owner has approved receiver for all"
119:  );
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol

```solidity
File: src/Vault.sol

165:  require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");

187:  require(msg.value > 0, "ZeroValue");
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol
