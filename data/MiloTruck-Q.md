# Low Issues

|No.|Issue|Instances|
|:-|:-|:-:|
|1|Use of `assert()` statements|1|
|2|Open TODOs|1|

Total: **2** instances over **2** issues

## `require()` should be used instead of `assert()` statements
Prior to Solidity v0.8.0, hitting an `assert()` statement **consumes all the remaining of the transaction's available gas** rather than returning it, as `require()`/`revert()` do.

Even past Solidity v0.8.0, `assert()` statements should be avoided, as seen in Solidity's [documentation](https://docs.soliditylang.org/en/v0.8.0/control-structures.html#panic-via-assert-and-error-via-require):
> Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix. Language analysis tools can evaluate your contract to identify the conditions and function calls which will cause a Panic.

Thus, consider using `require()` statements instead of `assert()`, or if-statements with `revert()`.

_There is **1** instance of this issue:_  
```solidity
src/Vault.sol:
 190:        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
```

## Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

_There is **1** instance of this issue:_  
```solidity
src/Vault.sol:
 195:        /**
 196:        @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
 197:        @param  id uint256 in UNIX timestamp, representing the end date of the epoch. Example: Epoch ends in 30th June 2022 at 00h 00min 00sec: 1654038000;
 198:        @param assets   uint256 of how many assets you want to withdraw, this value will be used to calculate how many assets you are entitle to according to the events;
 199:        @param receiver  Address of the receiver of the assets provided by this function, that represent the ownership of the transfered asset;
 200:        @param owner    Address of the owner of these said assets;
 201:        @return shares How many shares the owner is entitled to, according to the conditions;
 202:         */
```


# Non-Critical Issues

|No.|Issue|Instances|
|:-|:-|:-:|
|1|Adding a `return` statement when the function defines a named return variable, is redundant|1|
|2|`constants` should be defined rather than using magic numbers|13|
|3|Inconsistent spacing in comments|8|
|4|`event` is missing `indexed` fields|8|

Total: **30** instances over **4** issues

## Redundant `return` statements
If a function has defined return variables, they will be returned when the function terminates. Thus, there is no need to explicitly specify them in a return statement.

_There is **1** instance of this issue:_  
```solidity
src/Vault.sol:
 425:        return entitledAmount;
```

## `constants` should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) code can benefit from using readable constants instead of hex/numeric literals.

_There are **13** instances of this issue:_  
`18`:
```solidity
src/Controller.sol:
 299:        uint256 decimals = 10**(18-(priceFeed.decimals()));
```
`1000`:
```solidity
src/Vault.sol:
 266:        return (amount * epochFee[_epoch]) / 1000;
```
`150`:
```solidity
src/Vault.sol:
 311:        if(_withdrawalFee > 150)
```
`1 ether`:
```solidity
src/Vault.sol:
 402:        1 ether
```
`1 ether`:
```solidity
src/Vault.sol:
 409:        1 ether
```
`1 ether`:
```solidity
src/Vault.sol:
 421:        1 ether
```
`1 days`:
```solidity
src/Vault.sol:
 138:        timewindow = 1 days;
```
`1e18`:
```solidity
src/rewards/StakingRewards.sol:
 168:        .mul(1e18)
```
`1e18`:
```solidity
src/rewards/StakingRewards.sol:
 177:        .div(1e18)
```
`10000`:
```solidity
src/oracles/PegOracle.sol:
  68:        nowPrice = (price2 * 10000) / price1;
```
`10000`:
```solidity
src/oracles/PegOracle.sol:
  70:        nowPrice = (price1 * 10000) / price2;
```
`18`:
```solidity
src/oracles/PegOracle.sol:
  73:        int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
```
`1000000`:
```solidity
src/oracles/PegOracle.sol:
  78:        nowPrice / 1000000,
```

## Inconsistent spacing in comments
Some lines use `// <comment>` and some use `//<comment>`. The instances below point out the usages that don't follow the majority, within each file.

_There are **8** instances of this issue:_  
```solidity
src/Controller.sol:
 156:        //require this function cannot be called twice in the same epoch for the same vault
 214:        //require this function cannot be called twice in the same epoch for the same vault
```
```solidity
src/Vault.sol:
 225:        //Taking fee from the amount
 392:        //depeg event did not happen
 406:        //depeg event did happen
```
```solidity
src/VaultFactory.sol:
  11:        // solhint-disable var-name-mixedcase
  14:        // solhint-enable var-name-mixedcase
```
```solidity
src/rewards/RewardsFactory.sol:
  26:        // solhint-disable-next-line var-name-mixedcase
```

## `event` is missing `indexed` fields
Each `event` should use three `indexed` fields if there are three or more fields.
  
_There are **8** instances of this issue:_  
```solidity
src/Controller.sol:
  49:        event DepegInsurance(
  50:            bytes32 epochMarketID,
  51:            VaultTVL tvl,
  52:            bool isDisaster,
  53:            uint256 epoch,
  54:            uint256 time,
  55:            int256 depegPrice
  56:        );

src/SemiFungibleVault.sol:
  35:        event Deposit(
  36:            address caller,
  37:            address indexed owner,
  38:            uint256 indexed id,
  39:            uint256 assets,
  40:            uint256 shares
  41:        );


  51:        event Withdraw(
  52:            address caller,
  53:            address receiver,
  54:            address indexed owner,
  55:            uint256 indexed id,
  56:            uint256 assets,
  57:            uint256 shares
  58:        );

src/VaultFactory.sol:
  49:        event MarketCreated(
  50:            uint256 indexed mIndex,
  51:            address hedge,
  52:            address risk,
  53:            address token,
  54:            string name,
  55:            int256 strikePrice
  56:        );


  69:        event EpochCreated(
  70:            bytes32 indexed marketEpochId,
  71:            uint256 indexed mIndex,
  72:            uint256 startEpoch,
  73:            uint256 endEpoch,
  74:            address hedge,
  75:            address risk,
  76:            address token,
  77:            string name,
  78:            int256 strikePrice,
  79:            uint256 withdrawalFee
  80:        );

src/rewards/StakingRewards.sol:
  52:        event Staked(address indexed user, uint256 id, uint256 amount);
  53:        event Withdrawn(address indexed user, uint256 id, uint256 amount);

src/rewards/RewardsFactory.sol:
  39:        event CreatedStakingReward(
  40:            bytes32 indexed marketEpochId,
  41:            uint256 indexed mIndex,
  42:            address hedgeFarm,
  43:            address riskFarm
  44:        );
```
