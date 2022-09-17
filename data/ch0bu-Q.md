# LOW

# 1. `_safeMint()` should be used rather than `_mint()` wherever possible


`_mint()` is discouraged (https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both OpenZeppelin and solmate have versions of this function

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol
```
96		_mint(receiver, id, shares, EMPTY);
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol
```
169		_mint(receiver, id, shares, EMPTY);
```

# NON-CRITICAL

## 1. Event is missing `indexed`fields

Each `event` should use three `indexed` fields if there are three or more fields.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol
```
35	event Deposit(
36          address caller,
37          address indexed owner,
38          uint256 indexed id,
39          uint256 assets,
40          uint256 shares
...
51	event Withdraw(
52	    address caller,
53	    address receiver,
54	    address indexed owner,
55	    uint256 indexed id,
56	    uint256 assets,
57	    uint256 shares
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol
```
49	event MarketCreated(
50          uint256 indexed mIndex,
51          address hedge,
52          address risk,
53          address token,
54          string name,
55	    int256 strikePrice
...
69	event EpochCreated(
70          bytes32 indexed marketEpochId,
71          uint256 indexed mIndex,
72          uint256 startEpoch,
73          uint256 endEpoch,
74          address hedge,
75          address risk,
76          address token,
77          string name,
78          int256 strikePrice,
79          uint256 withdrawalFee
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol
```
39	event CreatedStakingReward(
40          bytes32 indexed marketEpochId,
41          uint256 indexed mIndex,
42          address hedgeFarm,
43          address riskFarm
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol
```
52		event Staked(address indexed user, uint256 id, uint256 amount);
53		event Withdrawn(address indexed user, uint256 id, uint256 amount);
```

## 2. File is missing NatSpec


It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI).

```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol
```

## 3. Missing event for critical parameter change

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol
```
350		function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
```

## 4. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom


It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol
```
190		assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
228		asset.transfer(treasury, feeValue);
231		asset.transfer(receiver, entitledShares);
365		asset.transfer(_counterparty, idFinalTVL[id]);
```