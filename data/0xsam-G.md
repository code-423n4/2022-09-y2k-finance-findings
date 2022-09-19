# Gas Optimization


## ++i costs less gas than i++ and i+=1 (--i/i--/i-=1 too)


        File: src/Vault.sol

        for (uint256 i = 0; i < epochsLength(); i++) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443

        File: src/VaultFactory.sol

        marketIndex += 1;

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L195




## Initializing a variable to its default value costs unnecessary gas.

        File: src/Vault.sol

        for (uint256 i = 0; i < epochsLength(); i++) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443

        File: src/VaultFactory.sol

        marketIndex = 0;

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L159


        File: src/rewards/StakingRewards.sol

    uint256 public periodFinish = 0;

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L36

        File: src/rewards/StakingRewards.sol

    uint256 public periodFinish = 0;


## Variable increment(e.g.++i/i++) for looping should be unchecked{++i} when they are not possible to overflow, to remove overflow checking to save gas.

        File: src/Vault.sol

        for (uint256 i = 0; i < epochsLength(); i++) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443


## Array length (or variable for looping condition check) should not be looked up in every loop. Instead, use a variable to store the length before the loop starts.

        File: src/Vault.sol

        for (uint256 i = 0; i < epochsLength(); i++) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443


## access state variable instead of using function to reduce gas.

        File: src/Vault.sol

        for (uint256 i = 0; i < epochsLength(); i++) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443


        File: src/Vault.sol

                if (i == epochsLength() - 1) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L445


## Declare errors for revert, instead of using string, to reduce gas.

        File: src/oracles/PegOracle.sol

        require(_oracle1 != address(0), "oracle1 cannot be the zero address");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L23

        File: src/oracles/PegOracle.sol

        require(_oracle2 != address(0), "oracle2 cannot be the zero address");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L24

        File: src/oracles/PegOracle.sol

        require(_oracle1 != _oracle2, "Cannot be same Oracle");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L25

        File: src/oracles/PegOracle.sol

        require(
            (priceFeed1.decimals() == priceFeed2.decimals()),
            "Decimals must be the same"
        );

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L30

        File: src/oracles/PegOracle.sol

        require(price1 > 0, "Chainlink price <= 0");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L98

        File: src/oracles/PegOracle.sol

        require(
            answeredInRound1 >= roundID1,
            "RoundID from Oracle is outdated!"
        );

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L101

        File: src/oracles/PegOracle.sol

        require(timeStamp1 != 0, "Timestamp == 0 !");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L103

        File: src/oracles/PegOracle.sol

        require(price2 > 0, "Chainlink price <= 0");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L121

        File: src/oracles/PegOracle.sol

        require(
            answeredInRound2 >= roundID2,
            "RoundID from Oracle is outdated!"
        );

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L124

        File: src/oracles/PegOracle.sol

        require(timeStamp2 != 0, "Timestamp == 0 !");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L126

        File: src/SemiFungibleVault.sol

        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L91

        File: src/SemiFungibleVault.sol

        require(
            msg.sender == owner || isApprovedForAll(owner, receiver),
            "Only owner can withdraw, or owner has approved receiver for all"
        );

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L118

        File: src/Vault.sol

        require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L165

        File: src/Vault.sol

        require(msg.value > 0, "ZeroValue");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L187

        File: src/rewards/StakingRewards.sol

        require(amount != 0, "Cannot stake 0");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L96

        File: src/rewards/StakingRewards.sol

        require(amount > 0, "Cannot withdraw 0");

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L119

        File: src/rewards/StakingRewards.sol

        require(
            rewardRate <= balance.div(rewardsDuration),
            "Provided reward too high"
        );

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L204

        File: src/rewards/StakingRewards.sol

        require(
            tokenAddress != address(stakingToken),
            "Cannot withdraw the staking token"
        );

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L219

        File: src/rewards/StakingRewards.sol

        require(
            block.timestamp > periodFinish,
            "Previous rewards period must be complete before changing the duration for the new period"
        );

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L228


## Consider inline the logic instead of using a modifier, which is only used once, to reduce contract size and deployment gas.

        File: src/rewards/StakingRewards.sol
    
    modifier isDisaster(uint256 marketIndex, uint256 epochEnd) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L83


        File: src/rewards/RewardsFactory.sol

    modifier onlyAdmin() {

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L52


## Consider removing modifier which is not used at all.

        File: src/Controller.sol

    modifier onlyAdmin() {

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L73


## Hardcode hash values instead of calculate keccak256() in runtime, to reduce gas.

        File: src/Vault.sol

            keccak256(abi.encodePacked("rY2K"))

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L389



## Make use of return variable to reduce gas.

        File: src/Controller.sol

BEFORE:

    function getLatestPrice(address _token)
        public
        view
        returns (int256 nowPrice)
    {
        (
            ,
            /*uint80 roundId*/
            int256 answer,
            uint256 startedAt, /*uint256 updatedAt*/ /*uint80 answeredInRound*/
            ,

        ) = sequencerUptimeFeed.latestRoundData();

        // Answer == 0: Sequencer is up
        // Answer == 1: Sequencer is down
        bool isSequencerUp = answer == 0;
        if (!isSequencerUp) {
            revert SequencerDown();
        }

        // Make sure the grace period has passed after the sequencer is back up.
        uint256 timeSinceUp = block.timestamp - startedAt;
        if (timeSinceUp <= GRACE_PERIOD_TIME) {
            revert GracePeriodNotOver();
        }

        AggregatorV3Interface priceFeed = AggregatorV3Interface(
            vaultFactory.tokenToOracle(_token)
        );
        (
            uint80 roundID,
            int256 price,
            ,
            uint256 timeStamp,
            uint80 answeredInRound
        ) = priceFeed.latestRoundData();

        uint256 decimals = 10**(18-(priceFeed.decimals()));
        price = price * int256(decimals);

        if(price <= 0)
            revert OraclePriceZero();

        if(answeredInRound < roundID)
            revert RoundIDOutdated();

        if(timeStamp == 0)
            revert TimestampZero();

        return price;
    }

AFTER:

    function getLatestPrice(address _token)
        public
        view
        returns (int256 price)
    {
        (
            ,
            /*uint80 roundId*/
            int256 answer,
            uint256 startedAt, /*uint256 updatedAt*/ /*uint80 answeredInRound*/
            ,

        ) = sequencerUptimeFeed.latestRoundData();

        // Answer == 0: Sequencer is up
        // Answer == 1: Sequencer is down
        bool isSequencerUp = answer == 0;
        if (!isSequencerUp) {
            revert SequencerDown();
        }

        // Make sure the grace period has passed after the sequencer is back up.
        uint256 timeSinceUp = block.timestamp - startedAt;
        if (timeSinceUp <= GRACE_PERIOD_TIME) {
            revert GracePeriodNotOver();
        }

        AggregatorV3Interface priceFeed = AggregatorV3Interface(
            vaultFactory.tokenToOracle(_token)
        );
        (
            uint80 roundID,
            price,
            ,
            uint256 timeStamp,
            uint80 answeredInRound
        ) = priceFeed.latestRoundData();

        uint256 decimals = 10**(18-(priceFeed.decimals()));
        price = price * int256(decimals);

        if(price <= 0)
            revert OraclePriceZero();

        if(answeredInRound < roundID)
            revert RoundIDOutdated();

        if(timeStamp == 0)
            revert TimestampZero();
    }


https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L264


