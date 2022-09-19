## L-01 Missing zero-address check in constructors

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L72-L87

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L63-L71

### Recommended Mitigation Steps

Consider adding zero-address checks in the discussed constructors

---------

## L-02 Using transfer and transferfrom function of an ERC721 contract may freeze the user's shares

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```
File: src/Vault.sol

167:    asset.transferFrom(msg.sender, address(this), shares);
190:    assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
228:    asset.transfer(treasury, feeValue);
231:    asset.transfer(receiver, entitledShares);
365:    asset.transfer(_counterparty, idFinalTVL[id]);
```

### Recommended Mitigation Steps

Consider using safeTransfer/safeTransferFrom or require() consistently.

________
## L-03 Require() should be used instead of assert()

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190

```
File: src/Vault.sol    Line: 190

assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
```

______________

## N-01 Adding a return statement when the function defines a named return variable, is redundant

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

--------

## N-02 Variable names that consist of all capital letters are reserved and should be used for const/immutable variables 

Constants and immutable variables should be named in all capital letters

### Proof of Concept

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol

```
File: src/Controller.sol

11:    address public immutable admin;
12:    VaultFactory public immutable vaultFactory;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L19

```
File: src/SemiFungibleVault.sol

19:    ERC20 public immutable asset;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```
File: src/Vault.sol

34:    address public immutable tokenInsured;
36:    int256 public immutable strikePrice;
37:    address private immutable factory;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol

```
File: src/VaultFactory.sol

12:    address public immutable Admin;
13:    address public immutable WETH;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol

```
File: src/rewards/StakingRewards.sol

34:    ERC20 public immutable rewardsToken;
35:    IERC1155 public immutable stakingToken;
```
_____________

## N-03 Use of TODO within natspec

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L196

```
File: src/Vault.sol

@notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
```

------------
## N-04 Use a more recent version of solidity

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives security fixes. Furthermore, breaking changes as well as new features are introduced regularly.

### Proof of Concept

All contracts are using the Solidity version 0.8.15 

### Recommended Mitigation Steps

Update to the latest released version of Solidity

------