
## 1. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom

It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

### Instances:
[Vault.sol:L190](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190)
[Vault.sol:L228](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L228)
[Vault.sol:L231](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L231)
[Vault.sol:L365](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L365)
[Vault.sol:L167](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L167)
```
src/Vault.sol:190:        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
src/Vault.sol:228:        asset.transfer(treasury, feeValue);
src/Vault.sol:231:        asset.transfer(receiver, entitledShares);
src/Vault.sol:365:        asset.transfer(_counterparty, idFinalTVL[id]);
src/Vault.sol:167:        asset.transferFrom(msg.sender, address(this), shares);
``` 
### Reference:

This [similar medium-severity finding](https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call) from Consensys Diligence Audit of Fei Protocol.

### Recommended Mitigation Steps:

Consider using safeTransfer/safeTransferFrom or require() consistently.


-----


## 2. _safeMint() should be used rather than _mint() wherever possible

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

### Instances
[Vault.sol:L169](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L169)
[SemiFungibleVault.sol:L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L96)
```
src/Vault.sol:169:        _mint(receiver, id, shares, EMPTY);
src/SemiFungibleVault.sol:96:        _mint(receiver, id, shares, EMPTY);
``` 
### Recommendations:

Use _safeMint() instead of _mint().


-----

## 3. Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

### Instances:
[Vault.sol:L196](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L196)
```
src/Vault.sol:196:    @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
``` 
### References:

[https://code4rena.com/reports/2022-05-sturdy/#l-09-open-todos](https://code4rena.com/reports/2022-05-sturdy/#l-09-open-todos)


-----


## 4. Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Instances:
[Controller.sol:L102](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L102)
[Controller.sol:L106](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L106)
[Controller.sol:L189](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L189)
[Controller.sol:L203](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L203)
[Controller.sol:L245](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L245)
[Controller.sol:L283](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L283)
[Vault.sol:L88](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L88)
[Vault.sol:L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L96)
[StakingRewards.sol:L156](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L156)
[StakingRewards.sol:L189](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L189)
[StakingRewards.sol:L192](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L192)
[StakingRewards.sol:L207](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L207)
[StakingRewards.sol:L208](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L208)
[StakingRewards.sol:L227](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L227)
```
src/Controller.sol:102:            vault.idEpochBegin(epochEnd) > block.timestamp)
src/Controller.sol:106:            block.timestamp > epochEnd
src/Controller.sol:189:            block.timestamp,
src/Controller.sol:203:            block.timestamp < epochEnd)
src/Controller.sol:245:            block.timestamp,
src/Controller.sol:283:        uint256 timeSinceUp = block.timestamp - startedAt;
src/Vault.sol:88:        if(block.timestamp > idEpochBegin[id] - timewindow)
src/Vault.sol:96:        if((block.timestamp < id) && idDepegged[id] == false)
src/rewards/StakingRewards.sol:156:        return block.timestamp < periodFinish ? block.timestamp : periodFinish;
src/rewards/StakingRewards.sol:189:        if (block.timestamp >= periodFinish) {
src/rewards/StakingRewards.sol:192:            uint256 remaining = periodFinish.sub(block.timestamp);
src/rewards/StakingRewards.sol:207:        lastUpdateTime = block.timestamp;
src/rewards/StakingRewards.sol:208:        periodFinish = block.timestamp.add(rewardsDuration);
src/rewards/StakingRewards.sol:227:            block.timestamp > periodFinish,
``` 
### References:

https://github.com/code-423n4/2022-04-dualityfocus-findings/issues/33


-----

## 5. Large multiples of ten should use scientific notation (e.g. `1e6`) rather than decimal literals (e.g. `1000000`), for readability

### Instances:
[PegOracle.sol:L78](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L78)
```
src/oracles/PegOracle.sol:78:            nowPrice / 1000000,
```