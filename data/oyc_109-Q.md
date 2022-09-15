## [L-01] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-09-y2k-finance/src/Controller.sol::102 => vault.idEpochBegin(epochEnd) > block.timestamp)
2022-09-y2k-finance/src/Controller.sol::106 => block.timestamp > epochEnd
2022-09-y2k-finance/src/Controller.sol::189 => block.timestamp,
2022-09-y2k-finance/src/Controller.sol::203 => block.timestamp < epochEnd)
2022-09-y2k-finance/src/Controller.sol::245 => block.timestamp,
2022-09-y2k-finance/src/Controller.sol::283 => uint256 timeSinceUp = block.timestamp - startedAt;
2022-09-y2k-finance/src/Vault.sol::88 => if(block.timestamp > idEpochBegin[id] - timewindow)
2022-09-y2k-finance/src/Vault.sol::96 => if((block.timestamp < id) && idDepegged[id] == false)
2022-09-y2k-finance/src/rewards/StakingRewards.sol::156 => return block.timestamp < periodFinish ? block.timestamp : periodFinish;
2022-09-y2k-finance/src/rewards/StakingRewards.sol::189 => if (block.timestamp >= periodFinish) {
2022-09-y2k-finance/src/rewards/StakingRewards.sol::192 => uint256 remaining = periodFinish.sub(block.timestamp);
2022-09-y2k-finance/src/rewards/StakingRewards.sol::207 => lastUpdateTime = block.timestamp;
2022-09-y2k-finance/src/rewards/StakingRewards.sol::208 => periodFinish = block.timestamp.add(rewardsDuration);
2022-09-y2k-finance/src/rewards/StakingRewards.sol::227 => block.timestamp > periodFinish,
```

## [L-02] Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

```
2022-09-y2k-finance/src/Vault.sol::196 => @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
```

## [L-03] decimals() not part of ERC20 standard

decimals() is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

```
2022-09-y2k-finance/src/Controller.sol::299 => uint256 decimals = 10**(18-(priceFeed.decimals()));
2022-09-y2k-finance/src/oracles/PegOracle.sol::29 => (priceFeed1.decimals() == priceFeed2.decimals()),
2022-09-y2k-finance/src/oracles/PegOracle.sol::36 => decimals = priceFeed1.decimals();
2022-09-y2k-finance/src/oracles/PegOracle.sol::73 => int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
```

## [L-04] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). Unless there is a compelling reason, abi.encode should be preferred. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

```
2022-09-y2k-finance/src/Vault.sol::388 => keccak256(abi.encodePacked(symbol)) ==
2022-09-y2k-finance/src/Vault.sol::389 => keccak256(abi.encodePacked("rY2K"))
2022-09-y2k-finance/src/VaultFactory.sol::278 => keccak256(abi.encodePacked(_marketVault.index, _marketVault.epochBegin, _marketVault.epochEnd)),
```

## [L-05] require() should be used instead of assert()

require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking

```
2022-09-y2k-finance/src/Vault.sol::190 => assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
```

## [L-06] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-09-y2k-finance/src/Vault.sol::167 => asset.transferFrom(msg.sender, address(this), shares);
2022-09-y2k-finance/src/Vault.sol::190 => assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
2022-09-y2k-finance/src/Vault.sol::228 => asset.transfer(treasury, feeValue);
2022-09-y2k-finance/src/Vault.sol::231 => asset.transfer(receiver, entitledShares);
2022-09-y2k-finance/src/Vault.sol::365 => asset.transfer(_counterparty, idFinalTVL[id]);
```

## [L-07] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
2022-09-y2k-finance/src/Vault.sol::350 => function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
```

## [L-08] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-09-y2k-finance/src/SemiFungibleVault.sol::96 => _mint(receiver, id, shares, EMPTY);
2022-09-y2k-finance/src/Vault.sol::169 => _mint(receiver, id, shares, EMPTY);
```

## [N-1] Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

```
2022-09-y2k-finance/src/oracles/PegOracle.sol::68 => nowPrice = (price2 * 10000) / price1;
2022-09-y2k-finance/src/oracles/PegOracle.sol::70 => nowPrice = (price1 * 10000) / price2;
2022-09-y2k-finance/src/oracles/PegOracle.sol::78 => nowPrice / 1000000,
```

## [N-2] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-09-y2k-finance/src/rewards/StakingRewards.sol::51 => event RewardAdded(uint256 reward);
2022-09-y2k-finance/src/rewards/StakingRewards.sol::55 => event RewardsDurationUpdated(uint256 newDuration);
2022-09-y2k-finance/src/rewards/StakingRewards.sol::56 => event Recovered(address token, uint256 amount);
```

## [N-3] Missing NatSpec

Code should include NatSpec

```
2022-09-y2k-finance/src/rewards/StakingRewards.sol::1 => // SPDX-License-Identifier: MIT
```

## [N-4] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public.

```
2022-09-y2k-finance/src/Controller.sol::198 => function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
2022-09-y2k-finance/src/SemiFungibleVault.sol::237 => function maxDeposit(address) public view virtual returns (uint256) {
2022-09-y2k-finance/src/SemiFungibleVault.sol::244 => function maxMint(address) public view virtual returns (uint256) {
2022-09-y2k-finance/src/Vault.sol::277 => function changeTreasury(address _treasury) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::287 => function changeTimewindow(uint256 _timewindow) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::295 => function changeController(address _controller) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::350 => function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
2022-09-y2k-finance/src/VaultFactory.sol::295 => function setController(address _controller) public onlyAdmin {
2022-09-y2k-finance/src/VaultFactory.sol::366 => function changeOracle(address _token, address _oracle) public onlyAdmin {
2022-09-y2k-finance/src/oracles/PegOracle.sol::89 => function getOracle1_Price() public view returns (int256 price) {
```

## [N-5] Adding a return statement when the function defines a named return variable, is redundant

It is not necessary to have both a named return and a return statement.

```
2022-09-y2k-finance/src/Controller.sol::264 => returns (int256 nowPrice)
2022-09-y2k-finance/src/Vault.sol::162 => returns (uint256 shares)
2022-09-y2k-finance/src/Vault.sol::185 => returns (uint256 shares)
2022-09-y2k-finance/src/Vault.sol::213 => returns (uint256 shares)
2022-09-y2k-finance/src/Vault.sol::263 => returns (uint256 feeValue)
2022-09-y2k-finance/src/Vault.sol::381 => returns (uint256 entitledAmount)
2022-09-y2k-finance/src/Vault.sol::441 => returns (uint256 nextEpochEnd)
2022-09-y2k-finance/src/oracles/PegOracle.sol::89 => function getOracle1_Price() public view returns (int256 price) {
2022-09-y2k-finance/src/oracles/PegOracle.sol::112 => function getOracle2_Price() public view returns (int256 price) {
2022-09-y2k-finance/src/rewards/RewardsFactory.sol::148 => returns (bytes32 hashedIndex)
```

## [N-6] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
2022-09-y2k-finance/src/Vault.sol::350 => function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
```

## [N-7] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2022-09-y2k-finance/src/Vault.sol::198 => @param assets   uint256 of how many assets you want to withdraw, this value will be used to calculate how many assets you are entitle to according to the events;
2022-09-y2k-finance/src/Vault.sol::304 => @param  epochEnd uint256 in UNIX timestamp, representing the end date of the epoch and also the ID for the minting functions. Example: Epoch ends in 30th June 2022 at 00h 00min 00sec: 1656630000
2022-09-y2k-finance/src/Vault.sol::346 => @notice Function to be called after endEpoch, by the Controller only, this function stores the TVL of the counterparty vault in a mapping to be used for later calculations of the entitled withdraw
2022-09-y2k-finance/src/Vault.sol::356 => @notice Function to be called after endEpoch and setClaimTVL functions, respecting the calls in order, after storing the TVL of the end of epoch and the TVL amount to claim, this function will allow the transfer of tokens to the counterparty vault
2022-09-y2k-finance/src/VaultFactory.sol::245 => @param  epochEnd uint256 in UNIX timestamp, representing the end date of the epoch and also the ID for the minting functions. Example: Epoch ends in 30th June 2022 at 00h 00min 00sec: 1656630000
```
