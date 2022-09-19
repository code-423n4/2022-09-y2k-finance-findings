
1.

Inconsistent spacing in code in:

Contract: Controller.sol

	line 299
	
Recommendation:

	uint256 decimals = 10**(18 - (priceFeed.decimals()));
	
This will also align with line 73 in Contract: PegOracle

2.

Inconsistent spacing in comments

Some comments that follow the second and sometimes first commented word or "//" statements do not align with each other throughout the contract bringing inconsistent reading with inconsistent spaces:

Recommendation:

Contract, lines of code, and consistent comment adjustments:

contract: Controller.sol

	line 156: // Require this function cannot be called twice in the same epoch for the same vault 
	line 214: // Require this function cannot be called twice in the same epoch for the same vault
	
contract: PegOracle.sol

	line 08: @dev for example: oracle1 would be stETH / USD, while oracle2 would be ETH / USD oracle

contract: Vault.sol

	line 107: @param  _assetAddress token address representing your asset to be deposited;
	line 108: @param  _name token name for the ERC1155 mints. Insert the name of your token; Example: Y2K_USDC_1.2$
	line 110: @param  _token address of the oracle to lookup the price in chainlink oracles;
	line 111: @param  _strikePrice uint256 representing the price to trigger the depeg event;
	line 112: @param _controller address of the controller contract, this contract can trigger the depeg events;
	line 147: @param  id uint256 in UNIX timestamp, representing the end date of the epoch. Example: Epoch ends in 30th June 2022 at 00h 00min 00sec: 1654038000;
	line 148: @param  assets uint256 representing how many assets the user wants to deposit, a fee will be taken from this value;
	line 178: @param  id uint256 in UNIX timestamp, representing the end date of the epoch. Example: Epoch ends in 30th June 2022 at 00h 00min 00sec: 1654038000;
	line 198: @param assets   uint256 of how many assets you want to withdraw, this value will be used to calculate how many assets you are entitle to according to the events;
	line 199: @param receiver Address of the receiver of the assets provided by this function, that represent the ownership of the transfered asset;
	line 200: @param owner Address of the owner of these said assets;
	line 225: // Taking fee from the amount
	line 392: // Depeg event did not happen
	line 406: // Depeg event did happen
	
contract: VaultFactory.sol

	line 175: @return insr Address of the deployed hedge vault
	line 176: @return rsk Address of the deployed risk vault
	line 197: // y2kUSDC_99*RISK or y2kUSDC_99*HEDGE
	
contract: RewardsFactory.sol

	line 25: // mapping(uint => mapping(uint => address[])) public marketIndex_epoch_StakingRewards; //Market Index, Epoch, Staking Rewards [0] = insrance, [1] = risk
	line 27: // Hashed Index, Staking Rewards [0] = insrance, [1] = risk
	
3.

It is best practice and also unnecessary to initialize the variable in for loops as they get set to 0 by default in:

Contract: Vault.sol

	line 443
	
Recommendation:

	for (uint256 i; i < epochsLength(); i++) {
	
4.

Consider using OpenZeppelin safe library when making an external call in:

Contract: Vault.sol
	
	line 167
	line 190
	line 228
	line 365
	
Recommendation:

	asset.safeTransferFrom(msg.sender, address(this), shares);
	assert(IWETH(address(asset)).safeTransfer(msg.sender, msg.value));
	asset.safeTransfer(treasury, feeValue);
	asset.safeTransfer(_counterparty, idFinalTVL[id]);