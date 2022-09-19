## [G-1]Cache  `epochsLength()` as it is reading epochs.length again and again:

```solidity
Vault.sol:443:     for (uint256 i = 0; i < epochsLength(); i++) {
```


## [G-2]Use preincrement to save gas
```solidity
Vault.sol:443:        for (uint256 i = 0; i < epochsLength(); i++) {
```

## [G-3]Don't use default values Explicit initialization with zero is not required for variable declaration of numTokens because `uints are 0` by default.removeing this will reduce contract size and save a bit of gas.

```solidity
Vault.sol:443:        for (uint256 i = 0; i < epochsLength(); i++) {
```


## [G-4]`public` functions not called by the contract should be declared `external` instead   
```solidity
Vault.sol:182:    function depositETH(uint256 id, address receiver)
Vault.sol:277:    function changeTreasury(address _treasury) public onlyFactory {
Vault.sol:287:    function changeTimewindow(uint256 _timewindow) public onlyFactory {
Vault.sol:295:    function changeController(address _controller) public onlyFactory {
rewards/StakingRewards.sol:132:    function getReward() public nonReentrant updateReward(msg.sender) {
rewards/StakingRewards.sol:155:    function lastTimeRewardApplicable() public view returns (uint256) {
rewards/StakingRewards.sol:159:    function rewardPerToken() public view returns (uint256) {
rewards/StakingRewards.sol:173:    function earned(address account) public view returns (uint256) {
oracles/PegOracle.sol:89:    function getOracle1_Price() public view returns (int256 price) {
oracles/PegOracle.sol:112:    function getOracle2_Price() public view returns (int256 price) {
SemiFungibleVault.sol:137:    function totalAssets(uint256 _id) public view virtual returns (uint256);
SemiFungibleVault.sol:237:    function maxDeposit(address) public view virtual returns (uint256) {
SemiFungibleVault.sol:244:    function maxMint(address) public view virtual returns (uint256) {
VaultFactory.sol:295:    function setController(address _controller) public onlyAdmin {
```

## [G-5] Use `!= 0` instead of `> 0` 
```solidity
Vault.sol:187:        require(msg.value > 0, "ZeroValue");
rewards/StakingRewards.sol:119:        require(amount > 0, "Cannot withdraw 0");
rewards/StakingRewards.sol:134:        if (reward > 0) {
oracles/PegOracle.sol:98:        require(price1 > 0, "Chainlink price <= 0");
oracles/PegOracle.sol:121:        require(price2 > 0, "Chainlink price <= 0");
```

## [G-6] Change `variable == false -> !variable` or `variable ==  true -> variable` to reduce gas
```solidity
Vault.sol:96:        if((block.timestamp < id) && idDepegged[id] == false)
Vault.sol:217:            isApprovedForAll(owner, receiver) == false)
Vault.sol:314:        if(idExists[epochEnd] == true)
rewards/RewardsFactory.sol:96:        if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
Controller.sol:93:        if(vault.idExists(epochEnd) == false)
Controller.sol:211:        if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
```

## [G-7] <x> += <y>` costs more gas than `<x> = <x> + <y>`
```solidity
VaultFactory.sol:195:        marketIndex += 1;

```