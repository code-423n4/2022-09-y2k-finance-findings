
### [L01] require() should be used instead of assert()



#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::190 => assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
```




### [L02] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-09-y2k-finance-main/src/Controller.sol::135 => admin = _admin;
2022-09-y2k-finance-main/src/Controller.sol::136 => vaultFactory = VaultFactory(_factory);
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::70 => asset = _asset;
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::71 => name = _name;
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::72 => symbol = _symbol;
2022-09-y2k-finance-main/src/Vault.sol::134 => treasury = _treasury;
2022-09-y2k-finance-main/src/Vault.sol::135 => strikePrice = _strikePrice;
2022-09-y2k-finance-main/src/Vault.sol::137 => controller = _controller;
2022-09-y2k-finance-main/src/Vault.sol::280 => treasury = _treasury;
2022-09-y2k-finance-main/src/Vault.sol::288 => timewindow = _timewindow;
2022-09-y2k-finance-main/src/Vault.sol::298 => controller = _controller;
2022-09-y2k-finance-main/src/VaultFactory.sol::157 => Admin = _admin;
2022-09-y2k-finance-main/src/VaultFactory.sol::160 => treasury = _treasury;
2022-09-y2k-finance-main/src/VaultFactory.sol::298 => controller = _controller;
2022-09-y2k-finance-main/src/VaultFactory.sol::312 => treasury = _treasury;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::33 => oracle1 = _oracle1;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::34 => oracle2 = _oracle2;
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::68 => admin = _admin;
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::69 => govToken = _govToken;
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::70 => factory = _factory;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::85 => rewardsDuration = _rewardsDuration;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::86 => rewardRate = _rewardRate;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::230 => rewardsDuration = _rewardsDuration;
```


### [L03] `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`

#### Impact
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::388 => keccak256(abi.encodePacked(symbol)) ==
2022-09-y2k-finance-main/src/Vault.sol::389 => keccak256(abi.encodePacked("rY2K"))
```





### [L04] `_safeMint()` should be used rather than `_mint()` wherever possible

#### Impact
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function

#### Findings:
```
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::96 => _mint(receiver, id, shares, EMPTY);
2022-09-y2k-finance-main/src/Vault.sol::169 => _mint(receiver, id, shares, EMPTY);
```






### [L05] Return values of transfer()/transferFrom() not checked

#### Impact
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean
 return value and they indicate errors that way instead. By not checking
 the return value, operations that should have marked as failed, may 
potentially go through without actually making a payment

#### Findings:
```
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::221 => ERC20(tokenAddress).safeTransfer(owner, tokenAmount);
```






### Non-Critical Issues


### [N01] Adding a return statement when the function defines a named return variable, is redundant



#### Findings:
```
2022-09-y2k-finance-main/src/Controller.sol::318 => return address(vaultFactory);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::144 => return _totalSupply;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::148 => return _balances[account];
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::152 => return rewardRate.mul(rewardsDuration);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::161 => return rewardPerTokenStored;
```




### [N02] constants should be defined rather than using magic numbers


#### Findings:
```
2022-09-y2k-finance-main/src/Controller.sol::299 => uint256 decimals = 10**(18-(priceFeed.decimals()));
2022-09-y2k-finance-main/src/Vault.sol::266 => return (amount * epochFee[_epoch]) / 1000;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::68 => nowPrice = (price2 * 10000) / price1;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::70 => nowPrice = (price1 * 10000) / price2;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::73 => int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::78 => nowPrice / 1000000,
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::168 => .mul(1e18)
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::177 => .div(1e18)
```



### [N03] Variable names that consist of all capital letters should be reserved for `const`/`immutable `variables

#### Impact
If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

#### Findings:
```
2022-09-y2k-finance-main/src/Controller.sol::13 => AggregatorV2V3Interface internal sequencerUptimeFeed;
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::22 => bytes internal constant EMPTY = "";
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::15 => AggregatorV3Interface internal priceFeed1;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::16 => AggregatorV3Interface internal priceFeed2;
```





### [N04] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
2022-09-y2k-finance-main/src/VaultFactory.sol::91 => event changedTreasury(address _treasury, uint256 indexed _marketIndex);
2022-09-y2k-finance-main/src/VaultFactory.sol::97 => event changedVaultFee(uint256 indexed _marketIndex, uint256 _feeRate);
2022-09-y2k-finance-main/src/VaultFactory.sol::103 => event changedTimeWindow(uint256 indexed _marketIndex, uint256 _timeWindow);
2022-09-y2k-finance-main/src/VaultFactory.sol::113 => event changedOracle(address indexed _token, address _oracle);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::52 => event Staked(address indexed user, uint256 id, uint256 amount);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::53 => event Withdrawn(address indexed user, uint256 id, uint256 amount);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::54 => event RewardPaid(address indexed user, uint256 reward);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::56 => event Recovered(address token, uint256 amount);
```


### [N05] Open TODOs

#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::196 => @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
```



### [N06] Unused file


#### Findings:
```
2022-09-y2k-finance-main/src/Controller.sol::1 => // SPDX-License-Identifier: MIT
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::1 => // SPDX-License-Identifier: MIT
2022-09-y2k-finance-main/src/Vault.sol::1 => // SPDX-License-Identifier: MIT
2022-09-y2k-finance-main/src/VaultFactory.sol::1 => // SPDX-License-Identifier: MIT
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::1 => // SPDX-License-Identifier: MIT
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::1 => // SPDX-License-Identifier: MIT
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::1 => // SPDX-License-Identifier: MIT
```








### [N07] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from external to public.

#### Findings:
```
2022-09-y2k-finance-main/src/Controller.sol::198 => function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::137 => function totalAssets(uint256 _id) public view virtual returns (uint256);
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::237 => function maxDeposit(address) public view virtual returns (uint256) {
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::244 => function maxMint(address) public view virtual returns (uint256) {
2022-09-y2k-finance-main/src/Vault.sol::277 => function changeTreasury(address _treasury) public onlyFactory {
2022-09-y2k-finance-main/src/Vault.sol::287 => function changeTimewindow(uint256 _timewindow) public onlyFactory {
2022-09-y2k-finance-main/src/Vault.sol::295 => function changeController(address _controller) public onlyFactory {
2022-09-y2k-finance-main/src/Vault.sol::350 => function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
2022-09-y2k-finance-main/src/Vault.sol::430 => function epochsLength() public view returns (uint256) {
2022-09-y2k-finance-main/src/VaultFactory.sol::295 => function setController(address _controller) public onlyAdmin {
2022-09-y2k-finance-main/src/VaultFactory.sol::366 => function changeOracle(address _token, address _oracle) public onlyAdmin {
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::89 => function getOracle1_Price() public view returns (int256 price) {
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::112 => function getOracle2_Price() public view returns (int256 price) {
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::132 => function getReward() public nonReentrant updateReward(msg.sender) {
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::155 => function lastTimeRewardApplicable() public view returns (uint256) {
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::159 => function rewardPerToken() public view returns (uint256) {
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::173 => function earned(address account) public view returns (uint256) {
```



### [N08] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks

#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::138 => timewindow = 1 days;
```










### [N09] Interfaces should be moved to separate files

#### Impact
Most of the other interfaces in this project are in their own file in
 the interfaces directory. The interfaces below do not follow this 
pattern

#### Findings:
```
2022-09-y2k-finance-main/src/VaultFactory.sol::6 => interface IController {
```




