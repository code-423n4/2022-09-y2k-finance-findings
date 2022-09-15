## none-critical issue foundings
### N01: EVENT IS MISSING INDEXED FIELDS
#### prof
src/Controller.sol, 56, b'    event DepegInsurance(\n        bytes32 epochMarketID,\n        VaultTVL tvl,\n        bool isDisaster,\n        uint256 epoch,\n        uint256 time,\n        int256 depegPrice\n    );'
src/rewards/StakingRewards.sol, 51, b'    event RewardAdded(uint256 reward);'
src/rewards/StakingRewards.sol, 55, b'    event RewardsDurationUpdated(uint256 newDuration);'
src/rewards/StakingRewards.sol, 56, b'    event Recovered(address token, uint256 amount);'

## low risks
### L01:_SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE
#### problem
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function
#### prof
src/SemiFungibleVault.sol, 96, b'        _mint(receiver, id, shares, EMPTY);'

### L02: address variable should check if it is zero MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES
#### prof
src/Controller.sol, 135, b'        admin = _admin;'
src/Controller.sol, 136, b'        vaultFactory = VaultFactory(_factory);'
src/Controller.sol, 137, b'        sequencerUptimeFeed = AggregatorV2V3Interface(_l2Sequencer);'
src/oracles/PegOracle.sol, 26, b'        priceFeed1 = AggregatorV3Interface(_oracle1);'
src/oracles/PegOracle.sol, 27, b'        priceFeed2 = AggregatorV3Interface(_oracle2);'
src/oracles/PegOracle.sol, 33, b'        oracle1 = _oracle1;'
src/oracles/PegOracle.sol, 34, b'        oracle2 = _oracle2;'
src/rewards/RewardsFactory.sol, 68, b'        admin = _admin;'
src/rewards/RewardsFactory.sol, 69, b'        govToken = _govToken;'
src/rewards/RewardsFactory.sol, 70, b'        factory = _factory;'
src/SemiFungibleVault.sol, 70, b'        asset = _asset;'
src/rewards/StakingRewards.sol, 81, b'        rewardsToken = ERC20(_rewardsToken);'
src/rewards/StakingRewards.sol, 82, b'        stakingToken = IERC1155(_stakingToken);'
src/SemiFungibleVault.sol, 70, b'        asset = _asset;'
src/VaultFactory.sol, 157, b'        Admin = _admin;'
src/VaultFactory.sol, 158, b'        WETH = _weth;'
src/VaultFactory.sol, 160, b'        treasury = _treasury;'
src/VaultFactory.sol, 298, b'        controller = _controller;'
src/VaultFactory.sol, 312, b'        treasury = _treasury;'

## low risks
### L03: the assert() function when false, uses up all the remaining gas and reverts all the changes made.
#### problem
assert(false) compiles to 0xfe, which is an invalid opcode, using up all remaining gas, and reverting all changes.
require(false) compiles to 0xfd which is the REVERT opcode, meaning it will refund the remaining gas. The opcode can also return a value (useful for debugging), but I don't believe that is supported in Solidity as of this moment. (2017-11-21)
#### prof
src/Vault.sol, 190, b'        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));'