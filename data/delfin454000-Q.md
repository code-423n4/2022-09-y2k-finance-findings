### Typos
___
[Vault.sol: L198](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L198)
```solidity
    @param assets   uint256 of how many assets you want to withdraw, this value will be used to calculate how many assets you are entitle to according to the events;
```
Change `entitle` to `entitled`
___
___

### Long single line comments 
In theory, comments over 79 characters should wrap using multi-line comment syntax. Even if somewhat longer comments are acceptable and a scroll bar is provided, there are cases where very long comments interfere with readability. Below are five instances of extra-long comments whose readability could be improved by wrapping, as shown:
___
[Vault.sol: L198](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L198)
```solidity
    @param assets   uint256 of how many assets you want to withdraw, this value will be used to calculate how many assets you are entitle to according to the events;
```
Suggestion:
```solidity
    @param assets   uint256 of how many assets you want to withdraw,
        this value will be used to calculate how many assets you are entitled to
        according to the events;
```
___
[Vault.sol: L346](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L346)
```solidity
    @notice Function to be called after endEpoch, by the Controller only, this function stores the TVL of the counterparty vault in a mapping to be used for later calculations of the entitled withdraw
```
Suggestion:
```solidity
    @notice Function to be called after endEpoch, by the Controller only, 
        this function stores the TVL of the counterparty vault in a mapping 
        to be used for later calculations of the entitled withdraw.
```
___
[Vault.sol: L356](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L356)
```solidity
    @notice Function to be called after endEpoch and setClaimTVL functions, respecting the calls in order, after storing the TVL of the end of epoch and the TVL amount to claim, this function will allow the transfer of tokens to the counterparty vault
```
Suggestion:
```solidity
    @notice Function to be called after endEpoch and setClaimTVL functions, 
         respecting the calls in order, after storing the TVL of the end of epoch
         and the TVL amount to claim, this function will allow the transfer of
         tokens to the counterparty vault.
```
___
The following line occurs three times:

[Vault.sol: L303](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L303)

[VaultFactory.sol: L172](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L172)

[VaultFactory.sol: L244](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L244)
```solidity
    @param  epochBegin uint256 in UNIX timestamp, representing the begin date of the epoch. Example: Epoch begins in 31/May/2022 at 00h 00min 00sec: 1654038000
```
Suggestion:
```solidity
    @param  epochBegin uint256 in UNIX timestamp, representing the begin date of the
       epoch. Example: Epoch begins in 31/May/2022 at 00h 00min 00sec: 1654038000.
```
___
The following line occurs twice:

[Vault.sol: L304](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L304)

[VaultFactory.sol: L245](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L245)
```solidity
    @param  epochEnd uint256 in UNIX timestamp, representing the end date of the epoch and also the ID for the minting functions. Example: Epoch ends in 30th June 2022 at 00h 00min 00sec: 1656630000
```
Suggestion:
```solidity
    @param  epochEnd uint256 in UNIX timestamp, representing the end date of
         the epoch and also the ID for the minting functions. Example: Epoch ends
         in 30th June 2022 at 00h 00min 00sec: 1656630000.
```
___
___

### Comments concerning unfinished work or open items should be addressed
Comments that contain TODOs or other language referring to open items should be addressed and modified or removed. Below is one instance:
___
[Vault.sol: L196](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L196)
```solidity
    @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
```
___
___

### Missing `@param` statements
___
[Vault.sol: L106-122](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L106-L122)
```solidity
        @notice constructor
        @param  _assetAddress    token address representing your asset to be deposited;
        @param  _name   token name for the ERC1155 mints. Insert the name of your token; Example: Y2K_USDC_1.2$
        @param  _symbol token symbol for the ERC1155 mints. insert here if risk or hedge + Symbol. Example: HedgeY2K or riskY2K;
        @param  _token  address of the oracle to lookup the price in chainlink oracles;
        @param  _strikePrice    uint256 representing the price to trigger the depeg event;
        @param _controller  address of the controller contract, this contract can trigger the depeg events;
     */
    constructor(
        address _assetAddress,
        string memory _name,
        string memory _symbol,
        address _treasury,
        address _token,
        int256 _strikePrice,
        address _controller
    ) SemiFungibleVault(ERC20(_assetAddress), _name, _symbol) {
```
`@param` statement missing for `_treasury`
___
[VaultFactory.sol: L42-56](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L42-L56)
```solidity
    /** @notice Market is created when event is emitted
      * @param mIndex Current market index
      * @param hedge Hedge vault address
      * @param risk Risk vault address
      * @param token Token address
      * @param name Market name
      */ 
    event MarketCreated(
        uint256 indexed mIndex,
        address hedge,
        address risk,
        address token,
        string name,
        int256 strikePrice
    );
```
`@param` statement missing for `strikePrice`
___
[VaultFactory.sol: L58-80](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L58-L80)
```solidity
    /** @notice Epoch is created when event is emitted
      * @param marketEpochId Current market epoch id
      * @param mIndex Current market index
      * @param startEpoch Epoch start time
      * @param endEpoch Epoch end time
      * @param hedge Hedge vault address
      * @param risk Risk vault address
      * @param token Token address
      * @param name Market name
      * @param strikePrice Vault strike price
      */
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
```
`@param` statement missing for `withdrawalFee`
___
[VaultFactory.sol: L168-186](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L168-L186)
```solidity
    @notice Function to create two new vaults, hedge and risk, with the respective params, and storing the oracle for the token provided
    @param _withdrawalFee uint256 of the fee value, multiply your % value by 10, Example: if you want fee of 0.5% , insert 5
    @param _token Address of the oracle to lookup the price in chainlink oracles
    @param _strikePrice uint256 representing the price to trigger the depeg event, needs to be 18 decimals
    @param  epochBegin uint256 in UNIX timestamp, representing the begin date of the epoch. Example: Epoch begins in 31/May/2022 at 00h 00min 00sec: 1654038000
    @param  epochEnd uint256 in UNIX timestamp, representing the end date of the epoch. Example: Epoch ends in 30th June 2022 at 00h 00min 00sec: 1656630000
    @param  _oracle Address representing the smart contract to lookup the price of the given _token param
    @return insr    Address of the deployed hedge vault
    @return rsk     Address of the deployed risk vault
     */
    function createNewMarket(
        uint256 _withdrawalFee,
        address _token,
        int256 _strikePrice,
        uint256 epochBegin,
        uint256 epochEnd,
        address _oracle,
        string memory _name
    ) public onlyAdmin returns (address insr, address rsk) {
```
`@param` statement missing for `_name`
___
[RewardsFactory.sol: L77-86](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L77-L86)
```solidity
    /** @notice Trigger staking rewards event
      * @param _marketIndex Target market index
      * @param _epochEnd End of epoch set for market
      * @return insr Insurance rewards address, first tuple address entry 
      * @return risk Risk rewards address, second tuple address entry 
      */
    function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate)
        external
        onlyAdmin
        returns (address insr, address risk)
```
`@param` statements missing for `_rewardDuration` and `_rewardRate`
___
___