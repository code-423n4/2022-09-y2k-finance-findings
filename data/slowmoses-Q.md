## (1) Consider using the most recent version of solidity

The project uses 0.8.15.
0.8.17 is already released.

## (2) There is an open TODO in the code

@notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L196

## (3) Missing indexed fields in events

Each event should use three indexed fields if there are three or more fields in the event.

***

event DepegInsurance(
    bytes32 epochMarketID,
    VaultTVL tvl,
    bool isDisaster,
    uint256 epoch,
    uint256 time,
    int256 depegPrice
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L49-L56

event Deposit(
    address caller,
    address indexed owner,
    uint256 indexed id,
    uint256 assets,
    uint256 shares
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L35-L41

event Withdraw(
    address caller,
    address receiver,
    address indexed owner,
    uint256 indexed id,
    uint256 assets,
    uint256 shares
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L51-L58

event MarketCreated(
    uint256 indexed mIndex,
    address hedge,
    address risk,
    address token,
    string name,
    int256 strikePrice
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L49-L56

event EpochCreated(
    bytes32 indexed marketEpochId,
    uint256 indexed mIndex,
    uint256 startEpoch,
    uint256 endEpoch,
    address hedge,
    address risk,
    address token,
    string name,
    int256 strikePrice,
    uint256 withdrawalFee
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L69-L80

event CreatedStakingReward(
    bytes32 indexed marketEpochId,
    uint256 indexed mIndex,
    address hedgeFarm,
    address riskFarm
);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L39-L44

