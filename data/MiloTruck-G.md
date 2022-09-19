# Gas Optimizations Report

|No.|Issue|Instances|
|:-|:-|:-:|
|1|For-loops: Index initialized with default value|1|
|2|For-Loops: Index increments can be left unchecked|1|
|3|Arithmetics: `++i` costs less gas compared to `i++` or `i += 1`|2|
|4|Arithmetics: Use `!= 0` instead of `> 0` for unsigned integers in `require` statements|4|
|5|Visibility: Consider declaring constants as non-public to save gas|1|
|6|Visibility: `public` functions can be set to `external`|13|
|7|Errors: Reduce the length of error messages (long revert strings)|5|
|8|Errors: Use custom errors instead of revert strings|19|
|9|Unnecessary initialization of variables with default values|1|
|10|Storage variables should be declared `immutable` when possible|13|
|11|Comparisons to boolean constants|6|
|12|State variables should be cached in stack variables rather than re-reading them from storage|11|
|13|`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables|1|
|14|Using `bool` for storage incurs overhead|2|
|15|Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead|10|
|16|Not using the named return variables when a function returns, wastes deployment gas|13|
|17|Multiple accesses of a mapping/array should use a local variable cache|3|

Total: **106** instances over **17** issues

## For-loops: Index initialized with default value
Uninitialized `uint` variables are assigned with a default value of `0`. 

Thus, in for-loops, explicitly initializing an index with `0` costs unnecesary gas. For example, the following code:
```js
for (uint256 i = 0; i < length; ++i) {
```
can be written without explicitly setting the index to `0`:  
```js
for (uint256 i; i < length; ++i) {
```

_There is **1** instance of this issue:_  
```solidity
src/Vault.sol:
 443:        for (uint256 i = 0; i < epochsLength(); i++) {
```

## For-Loops: Index increments can be left unchecked
From Solidity v0.8 onwards, all arithmetic operations come with implicit overflow and underflow checks. 

In for-loops, as it is impossible for the index to overflow, index increments can be left unchecked to save **[30-40 gas](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)** per loop iteration. 

For example, the code below:
```js
for (uint256 i; i < numIterations; ++i) {  
    // ...  
}  
```
can be changed to:
```js
for (uint256 i; i < numIterations;) {  
    // ...  
    unchecked { ++i; }  
}  
```

_There is **1** instance of this issue:_  
```solidity
src/Vault.sol:
 443:        for (uint256 i = 0; i < epochsLength(); i++) {
```

## Arithmetics: `++i` costs less gas compared to `i++` or `i += 1`
`++i` costs less gas compared to `i++` or `i += 1` for unsigned integers, as pre-increment is cheaper (about **5 gas** per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:
```js
uint i = 1;  
i++; // == 1 but i == 2  
```
But `++i` returns the actual incremented value:
```js
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`, thus it costs more gas.

The same logic applies for `--i` and `i--`.

_There are **2** instances of this issue:_  
```solidity
src/Vault.sol:
 443:        for (uint256 i = 0; i < epochsLength(); i++) {

src/VaultFactory.sol:
 195:        marketIndex += 1;
```

## Arithmetics: Use `!= 0` instead of `> 0` for unsigned integers in `require` statements
A variable of type `uint` will never go below 0. Thus, checking `!= 0` rather than `> 0` is sufficient, which would save **[6 gas](https://aws1.discourse-cdn.com/business6/uploads/zeppelin/original/2X/3/363a367d6d68851f27d2679d10706cd16d788b96.png)** per instance.

_There are **4** instances of this issue:_  
```solidity
src/Vault.sol:
 187:        require(msg.value > 0, "ZeroValue");

src/rewards/StakingRewards.sol:
 119:        require(amount > 0, "Cannot withdraw 0");

src/oracles/PegOracle.sol:
  98:        require(price1 > 0, "Chainlink price <= 0");
 121:        require(price2 > 0, "Chainlink price <= 0");
```

## Visibility: Consider declaring constants as non-public to save gas
If a constant is not used outside of its contract, declaring its visibility as `private` or `internal` instead of `public` can save gas. If needed, the value can be read from the verified contract source code.

Gas savings occur as the compiler does not have to create non-payable getter functions for deployment calldata and add another entry to the method ID table.

_There is **1** instance of this issue:_  
```solidity
src/Controller.sol:
  16:        uint256 public constant VAULTS_LENGTH = 2;
```

## Visibility: `public` functions can be set to `external`
Calls to `external` functions are cheaper than `public` functions. Thus, if a function is not used internally in any contract, it should be set to `external` to save gas and improve code readability.

_There are **13** instances of this issue:_  
```solidity
src/Controller.sol:
 148:        function triggerDepeg(uint256 marketIndex, uint256 epochEnd)
 149:            public
 150:            isDisaster(marketIndex, epochEnd)
 151:        {

 198:        function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {

src/Vault.sol:
 277:        function changeTreasury(address _treasury) public onlyFactory {
 287:        function changeTimewindow(uint256 _timewindow) public onlyFactory {
 295:        function changeController(address _controller) public onlyFactory {

 307:        function createAssets(uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee)
 308:            public
 309:            onlyFactory
 310:        {


 336:        function endEpoch(uint256 id, bool depeg)
 337:            public
 338:            onlyController
 339:            marketExists(id)
 340:        {

 350:        function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {

 360:        function sendTokens(uint256 id, address _counterparty)
 361:            public
 362:            onlyController
 363:            marketExists(id)
 364:        {


 438:        function getNextEpoch(uint256 _epoch)
 439:            public
 440:            view
 441:            returns (uint256 nextEpochEnd)
 442:        {

src/rewards/RewardsFactory.sol:
 145:        function getHashedIndex(uint256 _index, uint256 _epoch)
 146:            public
 147:            pure
 148:            returns (bytes32 hashedIndex)
 149:        {

src/oracles/PegOracle.sol:
  46:        function latestRoundData()
  47:            public
  48:            view
  49:            returns (
  50:                uint80 roundID,
  51:                int256 nowPrice,
  52:                uint256 startedAt,
  53:                uint256 timeStamp,
  54:                uint80 answeredInRound
  55:            )
  56:        {

  89:        function getOracle1_Price() public view returns (int256 price) {
```

## Errors: Reduce the length of error messages (long revert strings)
As seen [here](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings), revert strings longer than 32 bytes would incur an additional `mstore` (**3 gas**) for each extra 32-byte chunk. Furthermore, there are additional runtime costs for memory expansion and stack operations when the revert condition is met.

Thus, shortening revert strings to fit within 32 bytes would help to reduce runtime gas costs when the revert condition is met.

_There are **5** instances of this issue:_  
```solidity
src/SemiFungibleVault.sol:
 116:        require(
 117:            msg.sender == owner || isApprovedForAll(owner, receiver),
 118:            "Only owner can withdraw, or owner has approved receiver for all"
 119:        );

src/rewards/StakingRewards.sol:
 217:        require(
 218:            tokenAddress != address(stakingToken),
 219:            "Cannot withdraw the staking token"
 220:        );


 226:        require(
 227:            block.timestamp > periodFinish,
 228:            "Previous rewards period must be complete before changing the duration for the new period"
 229:        );

src/oracles/PegOracle.sol:
  23:        require(_oracle1 != address(0), "oracle1 cannot be the zero address");
  24:        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```

## Errors: Use custom errors instead of revert strings
Since Solidity v0.8.4, custom errors can be used rather than `require` statements.

Taken from [Custom Errors in Solidity](https://blog.soliditylang.org/2021/04/21/custom-errors/):
> Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., `revert("Insufficient funds.");`), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors reduce runtime gas costs as they save about **50 gas** each time they are hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Furthermore, not definiting error strings also help to reduce deployment gas costs. 

_There are **19** instances of this issue:_  
```solidity
src/SemiFungibleVault.sol:
  91:        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");

 116:        require(
 117:            msg.sender == owner || isApprovedForAll(owner, receiver),
 118:            "Only owner can withdraw, or owner has approved receiver for all"
 119:        );

src/Vault.sol:
 165:        require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
 187:        require(msg.value > 0, "ZeroValue");

src/rewards/StakingRewards.sol:
  96:        require(amount != 0, "Cannot stake 0");
 119:        require(amount > 0, "Cannot withdraw 0");

 202:        require(
 203:            rewardRate <= balance.div(rewardsDuration),
 204:            "Provided reward too high"
 205:        );


 217:        require(
 218:            tokenAddress != address(stakingToken),
 219:            "Cannot withdraw the staking token"
 220:        );


 226:        require(
 227:            block.timestamp > periodFinish,
 228:            "Previous rewards period must be complete before changing the duration for the new period"
 229:        );

src/oracles/PegOracle.sol:
  23:        require(_oracle1 != address(0), "oracle1 cannot be the zero address");
  24:        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
  25:        require(_oracle1 != _oracle2, "Cannot be same Oracle");

  28:        require(
  29:            (priceFeed1.decimals() == priceFeed2.decimals()),
  30:            "Decimals must be the same"
  31:        );

  98:        require(price1 > 0, "Chainlink price <= 0");

  99:        require(
 100:            answeredInRound1 >= roundID1,
 101:            "RoundID from Oracle is outdated!"
 102:        );

 103:        require(timeStamp1 != 0, "Timestamp == 0 !");
 121:        require(price2 > 0, "Chainlink price <= 0");

 122:        require(
 123:            answeredInRound2 >= roundID2,
 124:            "RoundID from Oracle is outdated!"
 125:        );

 126:        require(timeStamp2 != 0, "Timestamp == 0 !");
```

## Unnecessary initialization of variables with default values
Uninitialized variables are assigned with a default value depending on its type:
* `uint`: `0`
* `bool`: `false`
* `address`: `address(0)`

Thus, explicitly initializing a variable with its default value costs unnecesary gas. For example, the following code:
```js
bool b = false;
address c = address(0);
uint256 a = 0;
```
can be changed to:
```js
uint256 a;
bool b;
address c;
```

_There is **1** instance of this issue:_  
```solidity
src/rewards/StakingRewards.sol:
  36:        uint256 public periodFinish = 0;
```

## Storage variables should be declared `immutable` when possible
If a storage variable is assigned only in the constructor, it should be declared as `immutable`. This would help to reduce gas costs as calls to `immutable` variables are much cheaper than regular state variables, as seen from the [Solidity Docs](https://docs.soliditylang.org/en/v0.8.13/contracts.html#constant-and-immutable-state-variables):
> Compared to regular state variables, the gas costs of constant and immutable variables are much lower. Immutable variables are evaluated once at construction time and their value is copied to all the places in the code where they are accessed.

This helps to save gas as it avoids a Gsset (**20000 gas**) in the constructor, and replaces each Gwarmaccess (**100 gas**) with a `PUSH32` (**3 gas**).

_There are **13** instances of this issue:_  
```solidity
src/Controller.sol:
  13:        AggregatorV2V3Interface internal sequencerUptimeFeed;

src/SemiFungibleVault.sol:
  20:        string public name;
  21:        string public symbol;

src/VaultFactory.sol:
  17:        uint256 public marketIndex;

src/rewards/StakingRewards.sol:
  41:        uint256 public id;

src/rewards/RewardsFactory.sol:
   9:        address public admin;
  10:        address public govToken;
  11:        address public factory;

src/oracles/PegOracle.sol:
  10:        address public oracle1;
  11:        address public oracle2;
  13:        uint8 public decimals;
  15:        AggregatorV3Interface internal priceFeed1;
  16:        AggregatorV3Interface internal priceFeed2;
```

## Comparisons to boolean constants
Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the boolean value. Consider changing these comparisons from `<x> == true` to `<x>`, or `<x> == false` to `!<x>`:

_There are **6** instances of this issue:_  
```solidity
src/Controller.sol:
  93:        if(vault.idExists(epochEnd) == false)
 211:        if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)

src/Vault.sol:
  96:        if((block.timestamp < id) && idDepegged[id] == false)
 217:        isApprovedForAll(owner, receiver) == false)
 314:        if(idExists[epochEnd] == true)

src/rewards/RewardsFactory.sol:
  96:        if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
```

## State variables should be cached in stack variables rather than re-reading them from storage
If a state variable is read from storage multiple times in a function, it should be cached in a stack variable.

Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

_There are **11** instances of this issue:_  
`controller` in `createNewMarket()`:
```solidity
src/VaultFactory.sol:
 188:        IController(controller).getVaultFactory() != address(this)
 192:        if(controller == address(0))
 206:        controller
 216:        controller
```
`treasury` in `createNewMarket()`:
```solidity
src/VaultFactory.sol:
 203:        treasury,
 213:        treasury,
```
`marketIndex` in `createNewMarket()`:
```solidity
src/VaultFactory.sol:
 219:        indexVaults[marketIndex] = [address(hedge), address(risk)];

 225:        emit MarketCreated(
 226:            marketIndex,
 227:            address(hedge),
 228:            address(risk),
 229:            _token,
 230:            _name,
 231:            _strikePrice
 232:        );

 234:        MarketVault memory marketVault = MarketVault(marketIndex, epochBegin, epochEnd, hedge, risk, _withdrawalFee);
```
`id` in `stake()`:
```solidity
src/rewards/StakingRewards.sol:
 102:        id,
 106:        emit Staked(msg.sender, id, amount);
```
`id` in `withdraw()`:
```solidity
src/rewards/StakingRewards.sol:
 125:        id,
 129:        emit Withdrawn(msg.sender, id, amount);
```
`periodFinish` in `lastTimeRewardApplicable()`:
```solidity
src/rewards/StakingRewards.sol:
 156:        return block.timestamp < periodFinish ? block.timestamp : periodFinish;
 156:        return block.timestamp < periodFinish ? block.timestamp : periodFinish;
```
`_totalSupply` in `rewardPerToken()`:
```solidity
src/rewards/StakingRewards.sol:
 160:        if (_totalSupply == 0) {
 169:        .div(_totalSupply)
```
`rewardsDuration` in `notifyRewardAmount()`:
```solidity
src/rewards/StakingRewards.sol:
 190:        rewardRate = reward.div(rewardsDuration);
 194:        rewardRate = reward.add(leftover).div(rewardsDuration);
 203:        rewardRate <= balance.div(rewardsDuration),
 208:        periodFinish = block.timestamp.add(rewardsDuration);
```
`rewardRate` in `notifyRewardAmount()`:
```solidity
src/rewards/StakingRewards.sol:
 193:        uint256 leftover = remaining.mul(rewardRate);
 203:        rewardRate <= balance.div(rewardsDuration),
```
`admin` in `createStakingRewards()`:
```solidity
src/rewards/RewardsFactory.sol:
 100:        admin,
 101:        admin,
 109:        admin,
 110:        admin,
```
`govToken` in `createStakingRewards()`:
```solidity
src/rewards/RewardsFactory.sol:
 102:        govToken,
 111:        govToken,
```

## `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
The above also applies to state variables that use `-=`.

_There is **1** instance of this issue:_  
```solidity
src/VaultFactory.sol:
 195:        marketIndex += 1;
```

## Using `bool` for storage incurs overhead
Declaring storage variables as `bool` is more expensive compared to `uint256`, as explained [here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol#L23-L27):

> Booleans are more expensive than `uint256` or any type that takes up a full word because each write operation emits an extra `SLOAD` to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

Use `uint256(1)` and `uint256(2)` rather than true/false to avoid a Gwarmaccess ([**100 gas**](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)) for the extra `SLOAD`, and to avoid Gsset (**20000 gas**) when changing from 'false' to 'true', after having been 'true' in the past.

_There are **2** instances of this issue:_  
```solidity
src/Vault.sol:
  52:        mapping(uint256 => bool) public idDepegged;
  54:        mapping(uint256 => bool) public idExists;
```

## Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
As seen from [here](https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html):
> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

However, this does not apply to storage values as using reduced-size types might be beneficial to pack multiple elements into a single storage slot. Thus, where appropriate, use `uint256`/`int256` and downcast when needed.

_There are **10** instances of this issue:_  
```solidity
src/Controller.sol:
 292:        uint80 roundID,
 296:        uint80 answeredInRound

src/oracles/PegOracle.sol:
  50:        uint80 roundID,
  54:        uint80 answeredInRound
  58:        uint80 roundID1,
  62:        uint80 answeredInRound1
  91:        uint80 roundID1,
  95:        uint80 answeredInRound1
 114:        uint80 roundID2,
 118:        uint80 answeredInRound2
```

## Not using the named return variables when a function returns, wastes deployment gas  

_There are **13** instances of this issue:_  
```solidity
src/Controller.sol:
 311:        return price;

src/Vault.sol:
 192:        return deposit(id, msg.value, receiver);
 233:        return entitledShares;
 266:        return (amount * epochFee[_epoch]) / 1000;
 446:        return 0;
 448:        return epochs[i + 1];

src/VaultFactory.sol:
 238:        return (address(hedge), address(risk));
 390:        return indexVaults[index];

src/rewards/RewardsFactory.sol:
 137:        return (address(insrStake), address(riskStake));
 150:        return keccak256(abi.encode(_index, _epoch));

src/oracles/PegOracle.sol:
  76:        return (
  77:            roundID1,
  78:            nowPrice / 1000000,
  79:            startedAt1,
  80:            timeStamp1,
  81:            answeredInRound1
  82:        );

 105:        return price1;
 128:        return price2;
```

## Multiple accesses of a mapping/array should use a local variable cache
If a mapping/array is read with the same key/index multiple times in a function, it should be cached in a stack variable.

 Caching a mapping's value in a local `storage` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory.

_There are **3** instances of this issue:_  
`idFinalTVL[id]` in `beforeWithdraw()`:
```solidity
src/Vault.sol:
 400:        amount.divWadDown(idFinalTVL[id]).mulDivDown(
 407:        entitledAmount = amount.divWadDown(idFinalTVL[id]).mulDivDown(
 419:        entitledAmount = amount.divWadDown(idFinalTVL[id]).mulDivDown(
```
`idClaimTVL[id]` in `beforeWithdraw()`:
```solidity
src/Vault.sol:
 401:        idClaimTVL[id],
 408:        idClaimTVL[id],
 420:        idClaimTVL[id],
```
`indexVaults[index]` in `deployMoreAssets()`:
```solidity
src/VaultFactory.sol:
 260:        address hedge = indexVaults[index][0];
 261:        address risk = indexVaults[index][1];
```
