## G-01 Don't compare boolean expressions to boolean literals

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

### Proof of Concept

There are 7 instances of this issue

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol

```
File: src/Controller.sol

93:     if(vault.idExists(epochEnd) == false)
211:    if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```
File: src/Vault.sol

80:     if(idExists[id] != true)
96:     if((block.timestamp < id) && idDepegged[id] == false)
217:    isApprovedForAll(owner, receiver) == false)
314:    if(idExists[epochEnd] == true)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol

```
File: src/rewards/RewardsFactory.sol

96:     if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
```

___________

## G-02 Usage of uints or ints smaller than 32 bytes (26 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

### Proof of Concept

There is 1 instance of this issue

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L13

```
File: src/oracles/PegOracle.sol          Line: 13

uint8 public decimals;
```

---------------

## G-03 Use custom errors rather than revert() or require() strings to save deployment gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

Source: [https://blog.soliditylang.org/2021/04/21/custom-errors/](https://blog.soliditylang.org/2021/04/21/custom-errors/):

> Starting from [Solidity v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4), there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., `revert("Insufficient funds.");`), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the `error` statement, which can be used inside and outside of contracts (including interfaces and libraries).

### Proof of Concept

There are 19 instances of this issue

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol

```
File: src/oracles/PegOracle.sol

23:      require(_oracle1 != address(0), "oracle1 cannot be the zero address");
24:      require(_oracle2 != address(0), "oracle2 cannot be the zero address");
25:      require(_oracle1 != _oracle2, "Cannot be same Oracle");
28-30:   require((priceFeed1.decimals() == priceFeed2.decimals()),"Decimals must be the same"
98:      require(price1 > 0, "Chainlink price <= 0");
99-101:  require(answeredInRound1 >= roundID1, "RoundID from Oracle is outdated!"
103:     require(timeStamp1 != 0, "Timestamp == 0 !");
121:     require(price2 > 0, "Chainlink price <= 0");
122-124: require(answeredInRound2 >= roundID2, "RoundID from Oracle is outdated!"
126:     require(timeStamp2 != 0, "Timestamp == 0 !");
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol

```
File: src/SemiFungibleVault.sol

91:      require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
116-118: require(msg.sender == owner || isApprovedForAll(owner, receiver), "Only owner can withdraw, or owner has approved receiver for all"
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```
File: src/Vault.sol

165:     require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
187:     require(msg.value > 0, "ZeroValue");
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol

```
File: src/rewards/StakingRewards.sol

96:      require(amount != 0, "Cannot stake 0");
119:     require(amount > 0, "Cannot withdraw 0");
202-204: require(rewardRate <= balance.div(rewardsDuration), "Provided reward too high"
217-219: require(tokenAddress != address(stakingToken), "Cannot withdraw the staking token"
226-228: require(block.timestamp > periodFinish, "Previous rewards period must be complete before changing the duration for the new period"
```

-----

## G-04 It costs more gas to initialize variables to zero than to let the default of zero be applied

There are 3 instances of this issue

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

```
File: src/Vault.sol    Line: 443

for (uint256 i = 0; i < epochsLength(); i++) {
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L159

```
File: src/VaultFactory.sol    Line: 159

marketIndex = 0;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L36

```
File: src/rewards/StakingRewards.sol    Line: 36

uint256 public periodFinish = 0;
```

---------

## G-05 Using greater than 0 costs more gas than != 0 when used on a uint in a require() statement

!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas). This change saves 6 gas per instance

### Proof of Concept

There are 2 instances of this issue

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```
File: src/Vault.sol

187:    require(msg.value > 0, "ZeroValue");
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol

```
File: src/rewards/StakingRewards.sol

119:    require(amount > 0, "Cannot withdraw 0")
```

------------

## G-06 Use an unchecked block for calculations that cannot overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block:
https://docs.soliditylang.org/en/v0.8.7/control-structures.html#checked-or-unchecked-arithmetic

### Proof of Concept

There are 9 instances of this issue

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol

```
File: src/Controller.sol

283:    uint256 timeSinceUp = block.timestamp - startedAt;
299:    uint256 decimals = 10**(18-(priceFeed.decimals()));
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```
File: src/Vault.sol

88:      if(block.timestamp > idEpochBegin[id] - timewindow)
227:     entitledShares = entitledShares - feeValue;
266:     return (amount * epochFee[_epoch]) / 1000;
400-404: amount.divWadDown(idFinalTVL[id]).mulDivDown(idClaimTVL[id],1 ether) +
amount;
445:     if (i == epochsLength() - 1) {
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195

```
File: src/VaultFactory.sol

195:    marketIndex += 1;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L73

```
File: src/oracles/PegOracle.sol

73:    int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
```

--------------

## G-07 x += y costs more gas than x = x+y for state variables

There is 1 instance of this issue

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195

```
File: src/VaultFactory.sol

195:    marketIndex += 1;
```

---------

## G-08 ++i costs less gas than i++ especially when it's used in for loops (Same applies for --i , i-- too)

There is 1 instance of this issue

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

```
File: src/Vault.sol    Line: 443

for (uint256 i = 0; i < epochsLength(); i++) {
```

### Recommended Fix:

```
for (uint256 i = 0; i < epochsLength(); ++i) {
```
-------------

## G-09 Increments can be unchecked

There is 1 instance of this issue

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

```
File: src/Vault.sol    Line: 443

for (uint256 i = 0; i < epochsLength(); i++) {
```

-------------

## G-10 Require() or revert() strings longer than 32 bytes cost extra gas

There are 4 instances of this issue

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol

```
File: src/oracles/PegOracle.sol

23:      require(_oracle1 != address(0), "oracle1 cannot be the zero address");
24:      require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol

```
File: src/SemiFungibleVault.sol

116-118: require(msg.sender == owner || isApprovedForAll(owner, receiver), "Only owner can withdraw, or owner has approved receiver for all"
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol

```
File: src/rewards/StakingRewards.sol

226-228: require(block.timestamp > periodFinish, "Previous rewards period must be complete before changing the duration for the new period"
```

---------

## G-11 Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol

```
File: src/rewards/StakingRewards.sol

183-186: function notifyRewardAmount(uint256 reward) external override onlyRewardsDistribution
213-215: function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyOwner
225:     function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner {
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L83-L85

```
File: src/rewards/RewardsFactory.sol

83-85:    function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate) external onlyAdmin
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol

```
File: src/VaultFactory.sol

178-186:  function createNewMarket(uint256 _withdrawalFee, address _token, int256 _strikePrice, uint256 epochBegin, uint256 epochEnd, address _oracle, string memory _name) public onlyAdmin returns (address insr, address rsk) {
248-253:  function deployMoreAssets(uint256 index, uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee) public onlyAdmin {
295:      function setController(address _controller) public onlyAdmin {
308-310:  function changeTreasury(address _treasury, uint256 _marketIndex) public
onlyAdmin
327-329:  function changeTimewindow(uint256 _marketIndex, uint256 _timewindow) public onlyAdmin
345-347:  function changeController(uint256 _marketIndex, address _controller)
public onlyAdmin
366:      function changeOracle(address _token, address _oracle) public onlyAdmin {
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```
File: src/Vault.sol

277:     function changeTreasury(address _treasury) public onlyFactory {
287:     function changeTimewindow(uint256 _timewindow) public onlyFactory {
295:     function changeController(address _controller) public onlyFactory {
307-309: function createAssets(uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee) public onlyFactory
316-338: function endEpoch(uint256 id, bool depeg) public onlyController
350:     function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
360-362: function sendTokens(uint256 id, address _counterparty) public onlyController
```

-----------

## G-12 Using both named returns and a return statement isn’t necessary

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L261-L311

```
File: src/Controller.sol    function getLatestPrice()

function getLatestPrice(address _token) public view returns (int256 nowPrice)
{
...
return price;
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L46-L82

```
File: src/oracles/PegOracle.sol    function latestRoundData()

function latestRoundData() public view returns ( uint80 roundID, int256 nowPrice, uint256 startedAt, uint256 timeStamp, uint80 answeredInRound)
{
...
return (
roundID1,
nowPrice / 1000000,
startedAt1,
timeStamp1,
answeredInRound1
);
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L89-L106

```
File: src/oracles/PegOracle.sol    function getOracle1_Price()

function getOracle1_Price() public view returns (int256 price)
{
...
return price1;
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L112-L128

```
File: src/oracles/PegOracle.sol    function getOracle2_Price()

function getOracle2_Price() public view returns (int256 price)
{
...
return price2;
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L152-L174

```
File: src/Vault.sol    function deposit()

function deposit(uint256 id,uint256 assets,address receiver) public override marketExists(id) epochHasNotStarted(id) nonReentrant returns (uint256 shares)
{
...
return shares;
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L182-L193

```
File: src/Vault.sol    function depositETH()

function depositETH(uint256 id, address receiver) external payable returns (uint256 shares)
{
...
return deposit(id, msg.value, receiver);
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L203-L234

```
File: src/Vault.sol    function withdraw()

function withdraw(uint256 id,uint256 assets,address receiver,address owner)
external override epochHasEnded(id) marketExists(id) returns (uint256 shares)
{
...
return entitledShares;
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L260-L267

```
File: src/Vault.sol    function calculateWithdrawalFeeValue()

function calculateWithdrawalFeeValue(uint256 amount, uint256 _epoch) public
view returns (uint256 feeValue)
{
...
return (amount * epochFee[_epoch]) / 1000;
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L378-L426

```
File: src/Vault.sol    function beforeWithdraw()

function beforeWithdraw(uint256 id, uint256 amount) public view returns (uint256 entitledAmount)
{

...
return entitledAmount;
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L178-L239

```
File: src/VaultFactory.sol    function createNewMarket()

function createNewMarket(uint256 _withdrawalFee,address _token,int256 _strikePrice,
uint256 epochBegin,uint256 epochEnd,address _oracle,string memory _name) public onlyAdmin returns (address insr, address rsk)
{
...
return (address(hedge), address(risk));
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L385-L391

```
File: src/VaultFactory.sol    function getVaults()

function getVaults(uint256 index) public view returns (address[] memory vaults)
{
return indexVaults[index];
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L83-L138

```
File: src/rewards/RewardsFactory.sol    function createStakingRewards()

function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate) external onlyAdmin returns (address insr, address risk)
{
...
return (address(insrStake), address(riskStake));
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L145-L151

```
File: src/rewards/RewardsFactory.sol    function getHashedIndex()

function getHashedIndex(uint256 _index, uint256 _epoch) public pure returns (bytes32 hashedIndex)
{
...
return keccak256(abi.encode(_index, _epoch));
}
```

----------

## G-13 array.length should not be looked up in every loop of a for-loop

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration. 

It is suggested to store the array’s length in a variable before the for-loop, and use it instead:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

```
File: src/Vault.sol    Line: 

for (uint256 i = 0; i < epochsLength(); i++) {
```


-------------

## G-14 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol

```
File: src/SemiFungibleVault.sol

276-280:    function beforeWithdraw(uint256 id,uint256 assets,uint256 shares) internal virtual {}
282-286:    function afterDeposit(uint256 id,uint256 assets,uint256 shares) internal virtual {}
```

---------------
