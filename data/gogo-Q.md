# 2022-09-Y2K-FINANCE

## Low Risk and Non-Critical Issues

### Adding a `return` statement when the function defines a named return variable, is redundant

_There are **4** instances of this issue:_

```solidity
File: src/oracles/PegOracle.sol

89:   function getOracle1_Price() public view returns (int256 price) {
105:      return price1;

112:  function getOracle2_Price() public view returns (int256 price) {
128:      return price2;
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol

```solidity
File: src/Vault.sol

152:  function deposit(
173:      return shares;

378:  function beforeWithdraw(uint256 id, uint256 amount)
425:      return entitledAmount;
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol

### Events not emmited on important state changes

Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

_There are **3** instances of this issue:_

```solidity
File: src/Vault.sol

277:  function changeTreasury(address _treasury) public onlyFactory {

287:  function changeTimewindow(uint256 _timewindow) public onlyFactory {

295:  function changeController(address _controller) public onlyFactory {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol

### `public` functions not called by the contract should be declared `external` instead

_There are **26** instances of this issue:_

```solidity
File: src/Controller.sol

148:  function triggerDepeg(uint256 marketIndex, uint256 epochEnd)

198:  function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol

```solidity
File: src/oracles/PegOracle.sol

89:   function getOracle1_Price() public view returns (int256 price) {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol

```solidity
File: src/rewards/RewardsFactory.sol

145:  function getHashedIndex(uint256 _index, uint256 _epoch)
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol

```solidity
File: src/SemiFungibleVault.sol

85:   function deposit(

189:  function previewMint(uint256 id, uint256 shares)

221:  function previewRedeem(uint256 id, uint256 shares)

237:  function maxDeposit(address) public view virtual returns (uint256) {

244:  function maxMint(address) public view virtual returns (uint256) {

251:  function maxWithdraw(uint256 id, address owner)

263:  function maxRedeem(uint256 id, address owner)
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol

```solidity
File: src/Vault.sol

277:  function changeTreasury(address _treasury) public onlyFactory {

287:  function changeTimewindow(uint256 _timewindow) public onlyFactory {

295:  function changeController(address _controller) public onlyFactory {

307:  function createAssets(uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee)

336:  function endEpoch(uint256 id, bool depeg)

346:        @notice Function to be called after endEpoch, by the Controller only, this function stores the TVL of the counterparty vault in a mapping to be used for later calculations of the entitled withdraw

350:  function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {

356:        @notice Function to be called after endEpoch and setClaimTVL functions, respecting the calls in order, after storing the TVL of the end of epoch and the TVL amount to claim, this function will allow the transfer of tokens to the counterparty vault

360:  function sendTokens(uint256 id, address _counterparty)

438:  function getNextEpoch(uint256 _epoch)
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol

```solidity
File: src/VaultFactory.sol

178:  function createNewMarket(

248:  function deployMoreAssets(

295:  function setController(address _controller) public onlyAdmin {

366:  function changeOracle(address _token, address _oracle) public onlyAdmin {

385:  function getVaults(uint256 index)
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol

### Left ToDos

Code architecture, incentives and error handling questions should be resolved before deployment

_There are **1** instances of this issue:_

```solidity
File: src/Vault.sol

196:  @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol

### Use `1e18` instead of `10**18` or `1000000000000000000`

_There are **3** instances of this issue:_

```solidity
File: src/Controller.sol

299:  uint256 decimals = 10**(18-(priceFeed.decimals()));
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol

```solidity
File: src/oracles/PegOracle.sol

73:   int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));

78:   nowPrice / 1000000,
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol

### Natspec is missing

_There are **1** instances of this issue:_

```solidity
File: src/rewards/StakingRewards.sol

      /// @audit
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol

### Event is missing `indexed` fields

_There are **2** instances of this issue:_

```solidity
File: src/rewards/StakingRewards.sol

event      Staked(address indexed user, uint256 id, uint256 amount)

event      Withdrawn(address indexed user, uint256 id, uint256 amount)
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol

### Not used import

_There are **4** instances of this issue:_

```solidity
File: src/rewards/StakingRewards.sol

6:    import {

14:   import {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol

```solidity
File: src/SemiFungibleVault.sol

7:    import {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol

```solidity
File: src/Vault.sol

8:    import {
```

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol
