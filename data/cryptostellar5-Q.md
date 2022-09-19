### L-01 MISSING ZERO-ADDRESS CHECK IN THE CONSTRUCTOR

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L72-L87

```
 constructor(

        address _owner,//@audit-issue MISSING ZERO-ADDRESS CHECK

        address _rewardsDistribution,//@audit-issue MISSING ZERO-ADDRESS CHECK

        address _rewardsToken,//@audit-issue MISSING ZERO-ADDRESS CHECK

        address _stakingToken,//@audit-issue MISSING ZERO-ADDRESS CHECK

        uint256 _epochEnd,

        uint256 _rewardsDuration,

        uint256 _rewardRate

    ) Owned(_owner) {

        rewardsToken = ERC20(_rewardsToken);

        stakingToken = IERC1155(_stakingToken);

        rewardsDistribution = _rewardsDistribution;

        id = _epochEnd;

        rewardsDuration = _rewardsDuration;

        rewardRate = _rewardRate;

    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L63-L71

```
constructor(

address _govToken,

address _factory,

address _admin

) {

	admin = _admin;
	
	govToken = _govToken;
	
	factory = _factory;

}
```

### L-02 PREFER SAFETRANSFER AND SAFETRANSFERFROM

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L167
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L228
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L231
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L365


### L-03 NO EVENT IS RAISED

All important functions wherever some change is happening need to raise events

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L277
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L287
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L295
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L307
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L336
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L350
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L360


### N-01 USE LATEST SOLIDITY VERSION

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives [security fixes](https://github.com/ethereum/solidity/security/policy#supported-versions). Furthermore, breaking changes as well as new features are introduced regularly.
Latest Version is 0.8.17

This applies to all the contracts in scope.


### N-02 EVENT IS MISSING INDEXED FIELDS

Each `event` should use three `indexed` fields if there are three or more fields

[Controller.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol)

Events: 
- `DepegInsurance`

[SemiFungibleVault.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol)

Events:
- `Deposit`
- `Withdraw`

[VaultFactory.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol)

Events:

- `MarketCreated`
- `EpochCreated`
- `changedTreasury`
- `changedVaultFee`
- `changedTimeWindow`
- `changedOracle`

[RewardsFactory.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol)

Events: 

- `CreatedStakingReward`

[StakinRewards.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol)

Events: 

- `RewardAdded`
- `Staked`
- `Withdrawn`
- `RewardPaid`
- `RewardsDurationUpdated`
- `Recovered`


### N-03 `TODO` BEING USED

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L196
