# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Multiple address mappings can be combined into a single mapping of an address to a struct     | 3  |
| 2      | `indexVaults` mapping should use a fixed-length array to save gas    | 1   |
| 3      | State variables only set in the constructor should be declared `immutable`     | 5 |
| 4      | `epochsLength()` should not be looked up in every loop in the for loop |  1 |
| 5      | Use custom errors rather than `require()`/`revert()` strings to save deployments gas  |  19 |
| 6      | It costs more gas to initialize variables to zero than to let the default of zero be applied    |  3  |
| 7      | Use of `++i` cost less gas than `i++` in for loops    |  1  |
| 8      | `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops |  1  |
| 9      | Functions guaranteed to revert when called by normal users can be marked payable |  18 |

## Findings

### 1- Multiple address mappings can be combined into a single mapping of an address to a struct :

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 3 instances of this issue :

```
File: src/rewards/StakingRewards.sol

43      mapping(address => uint256) public userRewardPerTokenPaid;
44      mapping(address => uint256) public rewards;
47      mapping(address => uint256) private _balances;
```

These mappings could be refactored into the following struct and mapping for example :

```
struct User {
    uint256 userRewardPerTokenPaid;
    uint256 rewards;
    uint256 _balances;
}
    
mapping(address => User) public users;
```

### 2- `indexVaults` mapping should use a fixed-length array to save gas :

The `indexVaults` mapping stores the hedge & risk vault addresses for a given market index, because there will be always only two addresses to be stored `[address(hedge), address(risk)]` the mapping should use a fixed-length (static) array of 2 elements `address[2]` instead of a dynamic array, this will save ~20000 Gas when creating a new market.

There is 1 instance of this issue :

```
File: src/Vault.sol

119     mapping(uint256 => address[]) public indexVaults;
```

This should be replaced by :

```
mapping(uint256 => address[2]) public indexVaults;
```

### 3- State variables only set in the constructor should be declared `immutable` :

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

There are 5 instances of this issue:

```
File: src/SemiFungibleVault.sol

20      string public name;
21      string public symbol;

File: src/rewards/RewardsFactory.sol

17      address public admin;
18      address public govToken;
19      address public factory;
```

### 4- `epochsLength()` should not be looked up in every iteration in the for loop :

`epochsLength()` reads directely from storage thus costing more gas, it shouldn't be called at each iteration, its value should be cached into a memory variable

There is 1 instance of this issue:

```
File: src/Vault.sol

    for (uint256 i = 0; i < epochsLength(); i++) {
        if (epochs[i] == _epoch) {
            if (i == epochsLength() - 1) {
                return 0;
            }
            return epochs[i + 1];
        }
    }
```

This should be refactored as follow :

``` 
    uint256 len = epochsLength();
    for (uint256 i = 0; i < len; i++) {
        if (epochs[i] == _epoch) {
            if (i == len - 1) {
                return 0;
            }
            return epochs[i + 1];
        }
    }
```

### 5- Use custom errors rather than `require()`/`revert()` strings to save deployments gas :

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information.

There are 19 instances of this issue :

```
File: src/Vault.sol

165     require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
187     require(msg.value > 0, "ZeroValue");

File: src/SemiFungibleVault.sol

91      require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
116     require(
            msg.sender == owner || isApprovedForAll(owner, receiver),
            "Only owner can withdraw, or owner has approved receiver for all"
        );

File: src/rewards/StakingRewards.sol

96      require(amount != 0, "Cannot stake 0");
119     require(amount > 0, "Cannot withdraw 0");
202     require(
            rewardRate <= balance.div(rewardsDuration),
            "Provided reward too high"
        );
217     require(
            tokenAddress != address(stakingToken),
            "Cannot withdraw the staking token"
        );
226     require(
            block.timestamp > periodFinish,
            "Previous rewards period must be complete before changing the duration for the new period"
        );

File: src/oracles/PegOracle.sol

23      require(_oracle1 != address(0), "oracle1 cannot be the zero address");
24      require(_oracle2 != address(0), "oracle2 cannot be the zero address");
25      require(_oracle1 != _oracle2, "Cannot be same Oracle");
28      require(
            (priceFeed1.decimals() == priceFeed2.decimals()),
            "Decimals must be the same"
        );
98      require(price1 > 0, "Chainlink price <= 0");
99      require(
            answeredInRound1 >= roundID1,
            "RoundID from Oracle is outdated!"
        );
103     require(timeStamp1 != 0, "Timestamp == 0 !");
121     require(price2 > 0, "Chainlink price <= 0");
122     require(
            answeredInRound2 >= roundID2,
            "RoundID from Oracle is outdated!"
        );
126     require(timeStamp2 != 0, "Timestamp == 0 !");
```

### 6- It costs more gas to initialize variables to zero than to let the default of zero be applied :

If a variable is not set/initialized, it is assumed to have the default value (0 for uint or int, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

There are 3 instances of this issue:

```
File: src/rewards/StakingRewards.sol

36      uint256 public periodFinish = 0;

File: src/Vault.sol

443     for (uint256 i = 0; i < epochsLength(); i++)

File: src/VaultFactory.sol

159     marketIndex = 0;
```

### 7- Use of ++i cost less gas than i++/i=i+1 in for loops :

There is 1 instance of this issue:

```
File: src/Vault.sol

443     for (uint256 i = 0; i < epochsLength(); i++)
```

### 8- ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as in the case when used in for & while loops :

There is 1 instance of this issue:

```
File: src/Vault.sol

443     for (uint256 i = 0; i < epochsLength(); i++)
```

### 9- Functions guaranteed to revert when called by normal users can be marked **payable** :

If a function modifier such as **onlyAdmin** is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for the owner because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are : 

CALLVALUE(gas=2), DUP1(gas=3), ISZERO(gas=3), PUSH2(gas=3), JUMPI(gas=10), PUSH1(gas=3), DUP1(gas=3), REVERT(gas=0), JUMPDEST(gas=1), POP(gas=2). 
Which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 18 instances of this issue:

```
File: src/Vault.sol

227     function changeTreasury(address _treasury) public onlyFactory
287     function changeTimewindow(uint256 _timewindow) public onlyFactory
295     function changeController(address _controller) public onlyFactory
307     function createAssets(uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee) public onlyFactory
336     function endEpoch(uint256 id, bool depeg) public onlyController marketExists(id)
350     function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController
360     function sendTokens(uint256 id, address _counterparty) public onlyController marketExists(id)

File: src/VaultFactory.sol

178     function createNewMarket(uint256 _withdrawalFee, address _token, int256 _strikePrice, uint256 epochBegin, uint256 epochEnd, address _oracle, string memory _name) public onlyAdmin returns (address insr, address rsk)
248     function deployMoreAssets(uint256 index, uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee) public onlyAdmin
295     function setController(address _controller) public onlyAdmin
308     function changeTreasury(address _treasury, uint256 _marketIndex) public onlyAdmin
327     function changeTimewindow(uint256 _marketIndex, uint256 _timewindow) public onlyAdmin
345     function changeController(uint256 _marketIndex, address _controller) public onlyAdmin
366     function changeOracle(address _token, address _oracle) public onlyAdmin

File: src/rewards/RewardsFactory.sol

83      function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate)
        external
        onlyAdmin
        returns (address insr, address risk)

File: src/rewards/StakingRewards.sol

213     function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyOwner
225     function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner

File: src/rewards/RewardsDistributionRecipient.sol

20      function setRewardsDistribution(address _rewardsDistribution) external onlyOwner
```