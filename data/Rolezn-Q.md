## (1) Missing WhenNotPaused modifier

Severity: Low

Some external function have whenNotPasued modifier. However, several other external functions are missing the whenNotPaused modifiers and can potentially cause complications if called during pause.

## Proof of Concept
	function exit() external {
	        withdraw(_balances[msg.sender]);
	        getReward();
	    }
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L109-L112

    function withdraw(uint256 amount)
        public
        nonReentrant
        updateReward(msg.sender)
    {
        require(amount > 0, "Cannot withdraw 0");
        _totalSupply = _totalSupply.sub(amount);
        _balances[msg.sender] = _balances[msg.sender].sub(amount);
        stakingToken.safeTransferFrom(
            address(this),
            msg.sender,
            id,
            amount,
            ""
        );
        emit Withdrawn(msg.sender, id, amount);
    }
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L114-L130

    function getReward() public nonReentrant updateReward(msg.sender) {
        uint256 reward = rewards[msg.sender];
        if (reward > 0) {
            rewards[msg.sender] = 0;
            rewardsToken.safeTransfer(msg.sender, reward);
            emit RewardPaid(msg.sender, reward);
        }
    }
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L132-L139

## Impact
In case where the governance wants to stop all activity, some functions can still be called and could cause complications.

## Recommended Mitigation Steps
Add whenNotPaused to missing functions.

## (2) Withdraw event uses wrong parameter

Severity: Low

The event Withdraw states that:

	* @param assets Amount of owner assets to withdraw from vault
	* @param shares Amount of owner shares to burn
	
The Withdraw event in Vault.withdraw emits the 'assets' variable which is the initial, desired amount to withdraw. It should emit the actual withdrawn amount instead, which is transferred in the 'entitledShares' variable.
In addition, the shares parameter is also incorrect as it inputs the 'entitledShares' variable instead of the 'shares' variable which is the amount of 'shares' to burn.

## Proof of Concept

	emit Withdraw(msg.sender, receiver, owner, id, assets, entitledShares);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L230

## Impact

The actual withdrawn amount, which can be lower than assets , is part of the event.
This is usually not what you want (and it can already be decoded from the function argument).

## Recommended Mitigation Steps

Use it or remove it.


## (3) getOracle1_Price() is not being used by latestRoundData() in PegOracle.sol

Severity: Low

PegOracle.latestRoundData() does not use getOracle1_Price() which also extract oracle data using priceFeed1.latestRoundData().
PegOracle.latestRoundData() should call getOracle1_Price() and use its return data (with the additional values) instead of calling priceFeed1.latestRoundData() individually as getOracle1_Price() also performs oracle data feed validation.


## Proof Of Concept

    function latestRoundData()
        public
        view
        returns (
            uint80 roundID,
            int256 nowPrice,
            uint256 startedAt,
            uint256 timeStamp,
            uint80 answeredInRound
        )
    {
        (
            uint80 roundID1,
            int256 price1,
            uint256 startedAt1,
            uint256 timeStamp1,
            uint80 answeredInRound1
        ) = priceFeed1.latestRoundData();

        int256 price2 = getOracle2_Price();

        if (price1 > price2) {
            nowPrice = (price2 * 10000) / price1;
        } else {
            nowPrice = (price1 * 10000) / price2;
        }

        int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
        nowPrice = nowPrice * decimals10;

        return (
            roundID1,
            nowPrice / 1000000,
            startedAt1,
            timeStamp1,
            answeredInRound1
        );
    }
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L46-L83

    function getOracle1_Price() public view returns (int256 price) {
        (
            uint80 roundID1,
            int256 price1,
            ,
            uint256 timeStamp1,
            uint80 answeredInRound1
        ) = priceFeed1.latestRoundData();

        require(price1 > 0, "Chainlink price <= 0");
        require(
            answeredInRound1 >= roundID1,
            "RoundID from Oracle is outdated!"
        );
        require(timeStamp1 != 0, "Timestamp == 0 !");

        return price1;
    }
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L89-L106

## Recommended Mitigation Steps

PegOracle.latestRoundData() should call getOracle1_Price() and it should return:

    returns (
        uint80 roundID1,
        int256 price1,
        uint256 startedAt1,
        uint256 timeStamp1,
        uint80 answeredInRound1
    )


## (4) Use Safetransfer Instead Of Transfer 

Severity: Low

It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

Reference: This similar medium-severity (https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call) finding from Consensys Diligence Audit of Fei Protocol.

## Proof Of Concept


	asset.transferFrom(msg.sender, address(this), shares);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L167

	assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L190

	asset.transfer(treasury, feeValue);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L228

	asset.transfer(_counterparty, idFinalTVL[id]);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L365



## Recommended Mitigation Steps

Consider using safeTransfer/safeTransferFrom or require() consistently.



## (5) Constants Should Be Defined Rather Than Using Magic Numbers

Severity: Non-Critical

## Proof Of Concept

    if(_withdrawalFee > 150)
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L311

    nowPrice = (price2 * 10000) / price1;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L68



## (6) Event Is Missing Indexed Fields

Severity: Non-Critical

Each event should use three indexed fields if there are three or more fields.
In addition, each event should have at least one indexed fields to allow easier filtering of logs.

## Proof Of Concept


	event DepegInsurance(
        bytes32 epochMarketID,
        VaultTVL tvl,
        bool isDisaster,
        uint256 epoch,
        uint256 time,
        int256 depegPrice
    );
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L49

	event Deposit(
        address caller,
        address indexed owner,
        uint256 indexed id,
        uint256 assets,
        uint256 shares
    );
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L35

	event Withdraw(
        address caller,
        address receiver,
        address indexed owner,
        uint256 indexed id,
        uint256 assets,
        uint256 shares
    );
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L51

	event MarketCreated(
        uint256 indexed mIndex,
        address hedge,
        address risk,
        address token,
        string name,
        int256 strikePrice
    );
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L49

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
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L69

	event CreatedStakingReward(
        bytes32 indexed marketEpochId,
        uint256 indexed mIndex,
        address hedgeFarm,
        address riskFarm
    );
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L39

	event Staked(address indexed user, uint256 id, uint256 amount);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L52

	event Withdrawn(address indexed user, uint256 id, uint256 amount);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L53

	event Recovered(address token, uint256 amount);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L56






## (7) Inconsistent Spacing In Comments

Severity: Non-Critical

Some lines use // x and some use //x. The instances below point out the usages that don’t follow the majority, within each file

## Proof Of Concept


    //require this function cannot be called twice in the same epoch for the same vault
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L156

    @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L196

    //Taking fee from the amount
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L225

    //depeg event did not happen
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L392

    //depeg event did happen
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L406

    mapping(uint256 => uint256[]) public indexEpochs; //all epochs in the market
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L120

    mapping(address => address) public tokenToOracle; //token address to respective oracle smart contract address
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L121

    //y2kUSDC_99*RISK or y2kUSDC_99*HEDGE
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L197

    //mapping(uint => mapping(uint => address[])) public marketIndex_epoch_StakingRewards; //Market Index, Epoch, Staking Rewards [0] = insrance, [1] = risk
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L25

    mapping(bytes32 => address[]) public hashedIndex_StakingRewards; //Hashed Index, Staking Rewards [0] = insrance, [1] = risk
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L27

    // https://docs.synthetix.io/contracts/source/contracts/stakingrewards
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L21





## (8) Missing event for critical parameter change

Severity: Non-Critical

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

## Proof Of Concept


	function changeTreasury(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L277

	function changeTimewindow(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L287

	function changeController(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L295

	function setClaimTVL(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L350





## (9) Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

Severity: Non-Critical

## Proof Of Concept


	constructor(
        address _factory,
        address _admin,
        address _l2Sequencer
    ) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L121-L125

	constructor(
        address _assetAddress,
        string memory _name,
        string memory _symbol,
        address _treasury,
        address _token,
        int256 _strikePrice,
        address _controller
    ) SemiFungibleVault(ERC20(_assetAddress), _name, _symbol) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L114-L122

	constructor(
        address _treasury,
        address _weth,
        address _admin
    ) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L144-L148

	constructor(address _oracle1, address _oracle2) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L22

	constructor(
        address _govToken,
        address _factory,
        address _admin
    ) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L63-L67

	contract StakingRewards is
    IStakingRewards,
    RewardsDistributionRecipient,
    ReentrancyGuard,
    Pausable,
    ERC1155Holder

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L63-L67





## (10) Public Functions Not Called By The Contract Should Be Declared External Instead

Severity: Non-Critical

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

## Proof Of Concept


	function triggerDepeg(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L148

	function triggerEndEpoch(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L198

	function createNewMarket(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L178

	function deployMoreAssets(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L248

	function setController(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L295

	function changeOracle(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L366




## (11) Adding A Return Statement When The Function Defines A Named Return Variable, Is Redundant

Severity: Non-Critical

## Proof Of Concept


    function getVaultFactory() external view returns (address) {
        return address(vaultFactory);
    }
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L317


## (12) Large multiples of ten should use scientific notation

Severity: Non-Critical

Use (e.g. 1e6) rather than decimal literals (e.g. 100000), for better code readability.

## Proof Of Concept


    nowPrice = (price2 * 10000) / price1;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L68

    nowPrice = (price1 * 10000) / price2;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L70

    nowPrice / 1000000,
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L78




## (13) Use of Block.Timestamp

Severity: Non-Critical

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

## Proof Of Concept


    vault.idEpochBegin(epochEnd) > block.timestamp)
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L102

    block.timestamp > epochEnd
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L106

    block.timestamp < epochEnd)
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L203

    uint256 timeSinceUp = block.timestamp - startedAt;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L283

    if(block.timestamp > idEpochBegin[id] - timewindow)
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L88

    if((block.timestamp < id) && idDepegged[id] == false)
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L96

    return block.timestamp < periodFinish ? block.timestamp : periodFinish;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L156

    if (block.timestamp >= periodFinish) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L189

    uint256 remaining = periodFinish.sub(block.timestamp);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L192

    lastUpdateTime = block.timestamp;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L207

    periodFinish = block.timestamp.add(rewardsDuration);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L208

    block.timestamp > periodFinish,
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L227



## Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.


## (14) Open TODOs

Severity: Non-Critical

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

## Proof Of Concept

    @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L196


## (15) Commented Code in Controller.sol

Severity: Non-Critical

## Proof Of Concept
    (
        ,
        /*uint80 roundId*/
        int256 answer,
        uint256 startedAt, /*uint256 updatedAt*/ /*uint80 answeredInRound*/
        ,

    ) = sequencerUptimeFeed.latestRoundData();
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L266-L273

## (16) Insufficient NATSPEC documentation

Severity: Non-Critical

Function in StakingRewards.sol are lacking any NATSPEC documentation to its functions.

## Proof of Concept

For example:

    function stake(uint256 amount)
        external
        nonReentrant
        whenNotPaused
        updateReward(msg.sender)
    {
        require(amount != 0, "Cannot stake 0");
        _totalSupply = _totalSupply.add(amount);
        _balances[msg.sender] = _balances[msg.sender].add(amount);
        stakingToken.safeTransferFrom(
            msg.sender,
            address(this),
            id,
            amount,
            ""
        );
        emit Staked(msg.sender, id, amount);
    }
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L90-L107

## (17) Unused event may be unused code or indicative of missed emit/logic

Severity: Non-Critical

## Impact

Events that are declared but not used may be indicative of unused declarations where it makes sense to remove them for better readability/maintainability/auditability, or worse indicative of a missing emit which is bad for monitoring or missing logic that would have emitted that event.
Event changedVaultFee is missing an emit.

## Proof of Concept

	event changedVaultFee(uint256 indexed _marketIndex, uint256 _feeRate);
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L97

## Recommended Mitigation Steps

Add emit or remove event declaration.

## (18) Missing parameter validation

Severity: Non-Critical

Some parameters of functions are not checked for invalid values:
	• VaultFactory.constructor: all parameters could be zero

## Impact

Wrong user input or wallets defaulting to the zero addresses for a missing input can lead to the contract needing to redeploy or wasted gas.

## Proof of Concept

    constructor(
        address _govToken,
        address _factory,
        address _admin
    ) {
        admin = _admin;
        govToken = _govToken;
        factory = _factory;
    }
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L63-L71

## Recommended Mitigation Steps

Validate the parameters.

