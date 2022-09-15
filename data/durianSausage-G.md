## gas optimization
### G01: custom error save more gas
#### problem
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information, as explained https://blog.soliditylang.org/2021/04/21/custom-errors/. 
Custom errors are defined using the error statement.
#### prof
src/oracles/PegOracle.sol, 23, b'        require(_oracle1 != address(0), "oracle1 cannot be the zero address");'
src/oracles/PegOracle.sol, 24, b'        require(_oracle2 != address(0), "oracle2 cannot be the zero address");'
src/oracles/PegOracle.sol, 25, b'        require(_oracle1 != _oracle2, "Cannot be same Oracle");'
src/oracles/PegOracle.sol, 31, b'        require(\n            (priceFeed1.decimals() == priceFeed2.decimals()),\n            "Decimals must be the same"\n        );'
src/oracles/PegOracle.sol, 98, b'        require(price1 > 0, "Chainlink price <= 0");'
src/oracles/PegOracle.sol, 102, b'        require(\n            answeredInRound1 >= roundID1,\n            "RoundID from Oracle is outdated!"\n        );'
src/oracles/PegOracle.sol, 103, b'        require(timeStamp1 != 0, "Timestamp == 0 !");'
src/oracles/PegOracle.sol, 121, b'        require(price2 > 0, "Chainlink price <= 0");'
src/oracles/PegOracle.sol, 125, b'        require(\n            answeredInRound2 >= roundID2,\n            "RoundID from Oracle is outdated!"\n        );'
src/oracles/PegOracle.sol, 126, b'        require(timeStamp2 != 0, "Timestamp == 0 !");'
src/SemiFungibleVault.sol, 91, b'        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");'
src/SemiFungibleVault.sol, 119, b'        require(\n            msg.sender == owner || isApprovedForAll(owner, receiver),\n            "Only owner can withdraw, or owner has approved receiver for all"\n        );'
src/rewards/StakingRewards.sol, 96, b'        require(amount != 0, "Cannot stake 0");'
src/rewards/StakingRewards.sol, 119, b'        require(amount > 0, "Cannot withdraw 0");'
src/rewards/StakingRewards.sol, 205, b'        require(\n            rewardRate <= balance.div(rewardsDuration),\n            "Provided reward too high"\n        );'
src/rewards/StakingRewards.sol, 220, b'        require(\n            tokenAddress != address(stakingToken),\n            "Cannot withdraw the staking token"\n        );'
src/rewards/StakingRewards.sol, 229, b'        require(\n            block.timestamp > periodFinish,\n            "Previous rewards period must be complete before changing the duration for the new period"\n        );'
src/SemiFungibleVault.sol, 91, b'        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");'
src/SemiFungibleVault.sol, 119, b'        require(\n            msg.sender == owner || isApprovedForAll(owner, receiver),\n 

### G02: COMPARISONS WITH ZERO FOR UNSIGNED INTEGERS
#### problem
0 is less gas efficient than !0 if you enable the optimizer at 10k AND you’re in a require statement. Detailed explanation with the opcodes https://twitter.com/gzeon/status/1485428085885640706
#### prof
src/rewards/StakingRewards.sol, 119, b'        require(amount > 0, "Cannot withdraw 0");'
src/rewards/StakingRewards.sol, 134, b'        if (reward > 0) {'

### G03: X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES
#### prof
src/VaultFactory.sol, 195, b'        marketIndex += 1;'


### G04：ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
#### prof
src/rewards/RewardsFactory.sol, 118, b'        bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));'
src/rewards/RewardsFactory.sol, 150, b'        return keccak256(abi.encode(_index, _epoch));'


### G05: Splitting require() statements that use && saves gas
#### problem
See this issue(https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper
#### prof:
src/SemiFungibleVault.sol, 119, b'        require(\n            msg.sender == owner || isApprovedForAll(owner, receiver),\n            "Only owner can withdraw, or owner has approved receiver for all"\n        );'

### G06:FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
#### problem
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
#### prof
src/rewards/RewardsFactory.sol, 138, b'    function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate)\n        external\n        onlyAdmin\n        returns (address insr, address risk)\n    '
src/rewards/RewardsFactory.sol, 138, b'    function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate)\n        external\n        onlyAdmin\n        returns (address insr, address risk)\n    '
src/SemiFungibleVault.sol, 128, b'    function withdraw(\n        uint256 id,\n        uint256 assets,\n        address receiver,\n        address owner\n    ) external virtual returns (uint256 shares) '
src/SemiFungibleVault.sol, 128, b'    function withdraw(\n        uint256 id,\n        uint256 assets,\n        address receiver,\n        address owner\n    ) external virtual returns (uint256 shares) '
src/SemiFungibleVault.sol, 258, b'    function maxWithdraw(uint256 id, address owner)\n        public\n        view\n        virtual\n        returns (uint256)\n    '
src/SemiFungibleVault.sol, 258, b'    function maxWithdraw(uint256 id, address owner)\n        public\n        view\n        virtual\n        returns (uint256)\n    '
src/SemiFungibleVault.sol, 270, b'    function maxRedeem(uint256 id, address owner)\n        public\n        view\n        virtual\n        returns (uint256)\n    '
src/SemiFungibleVault.sol, 270, b'    function maxRedeem(uint256 id, address owner)\n        public\n        view\n        virtual\n        returns (uint256)\n    '
src/rewards/StakingRewards.sol, 223, b'    function recoverERC20(address tokenAddress, uint256 tokenAmount)\n        external\n        onlyOwner\n    '
src/rewards/StakingRewards.sol, 223, b'    function recoverERC20(address tokenAddress, uint256 tokenAmount)\n        external\n        onlyOwner\n    '
src/rewards/StakingRewards.sol, 232, b'    function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner '
src/rewards/StakingRewards.sol, 232, b'    function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner '
src/SemiFungibleVault.sol, 128, b'    function withdraw(\n        uint256 id,\n        uint256 assets,\n        address receiver,\n        address owner\n    ) external virtual returns (uint256 shares) '
src/SemiFungibleVault.sol, 128, b'    function withdraw(\n        uint256 id,\n        uint256 assets,\n        address receiver,\n        address owner\n    ) external virtual returns (uint256 shares) '
src/SemiFungibleVault.sol, 258, b'    function maxWithdraw(uint256 id, address owner)\n        public\n        view\n        virtual\n        returns (uint256)\n    '
src/SemiFungibleVault.sol, 258, b'    function maxWithdraw(uint256 id, address owner)\n        public\n        view\n        virtual\n        returns (uint256)\n    '
src/SemiFungibleVault.sol, 270, b'    function maxRedeem(uint256 id, address owner)\n        public\n        view\n        virtual\n        returns (uint256)\n    '
src/SemiFungibleVault.sol, 270, b'    function maxRedeem(uint256 id, address owner)\n        public\n        view\n        virtual\n        returns (uint256)\n    '
src/VaultFactory.sol, 239, b'    function createNewMarket(\n        uint256 _withdrawalFee,\n        address _token,\n        int256 _strikePrice,\n        uint256 epochBegin,\n        uint256 epochEnd,\n        address _oracle,\n        string memory _name\n    ) public onlyAdmin returns (address insr, address rsk) '
src/VaultFactory.sol, 239, b'    function createNewMarket(\n        uint256 _withdrawalFee,\n        address _token,\n        int256 _strikePrice,\n        uint256 epochBegin,\n        uint256 epochEnd,\n        address _oracle,\n        string memory _name\n    ) public onlyAdmin returns (address insr, address rsk) '
src/VaultFactory.sol, 266, b'    function deployMoreAssets(\n        uint256 index,\n        uint256 epochBegin,\n        uint256 epochEnd,\n        uint256 _withdrawalFee\n    ) public onlyAdmin '
src/VaultFactory.sol, 266, b'    function deployMoreAssets(\n        uint256 index,\n        uint256 epochBegin,\n        uint256 epochEnd,\n        uint256 _withdrawalFee\n    ) public onlyAdmin '
src/VaultFactory.sol, 301, b'    function setController(address _controller) public onlyAdmin '
src/VaultFactory.sol, 301, b'    function setController(address _controller) public onlyAdmin '
src/VaultFactory.sol, 320, b'    function changeTreasury(address _treasury, uint256 _marketIndex)\n        public\n        onlyAdmin\n    '
src/VaultFactory.sol, 338, b'    function changeTimewindow(uint256 _marketIndex, uint256 _timewindow)\n        public\n        onlyAdmin\n    '
src/VaultFactory.sol, 359, b'    function changeController(uint256 _marketIndex, address _controller)\n        public\n        onlyAdmin\n    '
src/VaultFactory.sol, 374, b'    function changeOracle(address _token, address _oracle) public onlyAdmin '
src/VaultFactory.sol, 374, b'    function changeOracle(address _token, address _oracle) public onlyAdmin '

### G07: USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
#### problem
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table
#### prof
src/Controller.sol, 11, b'    address public immutable admin;'
src/Controller.sol, 12, b'    VaultFactory public immutable vaultFactory;'
src/Controller.sol, 16, b'    uint256 public constant VAULTS_LENGTH = 2;'
src/SemiFungibleVault.sol, 19, b'    ERC20 public immutable asset;'
src/rewards/StakingRewards.sol, 34, b'    ERC20 public immutable rewardsToken;'
src/rewards/StakingRewards.sol, 35, b'    IERC1155 public immutable stakingToken;'
src/SemiFungibleVault.sol, 19, b'    ERC20 public immutable asset;'
src/VaultFactory.sol, 12, b'    address public immutable Admin;'
src/VaultFactory.sol, 13, b'    address public immutable WETH;'

### G08: USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
#### problem
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
#### prof

src/Controller.sol, 126, b'        if(_admin == address(0))'
src/Controller.sol, 129, b'        if(_factory == address(0)) '
src/Controller.sol, 132, b'        if(_l2Sequencer == address(0))'
src/Controller.sol, 157, b'        if(insrVault.idFinalTVL(epochEnd) != 0)'
src/Controller.sol, 159, b'        if(riskVault.idFinalTVL(epochEnd) != 0) '
src/Controller.sol, 171, b'        VaultTVL memory tvl = VaultTVL('
src/Controller.sol, 215, b'        if(insrVault.idFinalTVL(epochEnd) != 0)'
src/Controller.sol, 217, b'        if(riskVault.idFinalTVL(epochEnd) != 0) '
src/Controller.sol, 223, b'        insrVault.setClaimTVL(epochEnd, 0);'
src/Controller.sol, 227, b'        VaultTVL memory tvl = VaultTVL('
src/Controller.sol, 277, b'        bool isSequencerUp = answer == 0;'
src/Controller.sol, 292, b'            uint80 roundID,'
src/Controller.sol, 296, b'            uint80 answeredInRound'
src/Controller.sol, 299, b'        uint256 decimals = 10**(18-(priceFeed.decimals()));'
src/Controller.sol, 299, b'        uint256 decimals = 10**(18-(priceFeed.decimals()));'
src/Controller.sol, 302, b'        if(price <= 0)'
src/Controller.sol, 305, b'        if(answeredInRound < roundID)'
src/Controller.sol, 305, b'        if(answeredInRound < roundID)'
src/Controller.sol, 308, b'        if(timeStamp == 0)'
src/oracles/PegOracle.sol, 13, b'    uint8 public decimals;'
src/oracles/PegOracle.sol, 23, b'        require(_oracle1 != address(0), "oracle1 cannot be the zero address");'
src/oracles/PegOracle.sol, 24, b'        require(_oracle2 != address(0), "oracle2 cannot be the zero address");'
src/oracles/PegOracle.sol, 36, b'        decimals = priceFeed1.decimals();'
src/oracles/PegOracle.sol, 58, b'            uint80 roundID1,'
src/oracles/PegOracle.sol, 62, b'            uint80 answeredInRound1'
src/oracles/PegOracle.sol, 73, b'        int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));'
src/oracles/PegOracle.sol, 73, b'        int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));'
src/oracles/PegOracle.sol, 91, b'            uint80 roundID1,'
src/oracles/PegOracle.sol, 95, b'            uint80 answeredInRound1'
src/oracles/PegOracle.sol, 98, b'        require(price1 > 0, "Chainlink price <= 0");'
src/oracles/PegOracle.sol, 100, b'            answeredInRound1 >= roundID1,'
src/oracles/PegOracle.sol, 100, b'            answeredInRound1 >= roundID1,'
src/oracles/PegOracle.sol, 103, b'        require(timeStamp1 != 0, "Timestamp == 0 !");'
src/oracles/PegOracle.sol, 114, b'            uint80 roundID2,'
src/oracles/PegOracle.sol, 118, b'            uint80 answeredInRound2'
src/oracles/PegOracle.sol, 121, b'        require(price2 > 0, "Chainlink price <= 0");'
src/oracles/PegOracle.sol, 123, b'            answeredInRound2 >= roundID2,'
src/oracles/PegOracle.sol, 123, b'            answeredInRound2 >= roundID2,'
src/oracles/PegOracle.sol, 126, b'        require(timeStamp2 != 0, "Timestamp == 0 !");'
src/rewards/RewardsFactory.sol, 93, b'        if(_insrToken == address(0) || _riskToken == address(0))'
src/rewards/RewardsFactory.sol, 93, b'        if(_insrToken == address(0) || _riskToken == address(0))'
src/SemiFungibleVault.sol, 91, b'        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");'
src/SemiFungibleVault.sol, 152, b'            supply == 0 ? assets : assets.mulDivDown(supply, totalAssets(id));'
src/SemiFungibleVault.sol, 167, b'            supply == 0 ? shares : shares.mulDivDown(totalAssets(id), supply);'
src/SemiFungibleVault.sol, 197, b'        return supply == 0 ? shares : shares.mulDivUp(totalAssets(id), supply);'
src/SemiFungibleVault.sol, 213, b'        return supply == 0 ? assets : assets.mulDivUp(supply, totalAssets(id));'
src/rewards/StakingRewards.sol, 96, b'        require(amount != 0, "Cannot stake 0");'
src/rewards/StakingRewards.sol, 119, b'        require(amount > 0, "Cannot withdraw 0");'
src/rewards/StakingRewards.sol, 134, b'        if (reward > 0) {'
src/rewards/StakingRewards.sol, 160, b'        if (_totalSupply == 0) {'
src/rewards/StakingRewards.sol, 168, b'                    .mul(1e18)'
src/rewards/StakingRewards.sol, 177, b'                .div(1e18)'
src/SemiFungibleVault.sol, 91, b'        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");'
src/SemiFungibleVault.sol, 152, b'            supply == 0 ? assets : assets.mulDivDown(supply, totalAssets(id));'
src/SemiFungibleVault.sol, 167, b'            supply == 0 ? shares : shares.mulDivDown(totalAssets(id), supply);'
src/SemiFungibleVault.sol, 197, b'        return supply == 0 ? shares : shares.mulDivUp(totalAssets(id), supply);'
src/SemiFungibleVault.sol, 213, b'        return supply == 0 ? assets : assets.mulDivUp(supply, totalAssets(id));'
src/VaultFactory.sol, 149, b'        if(_admin == address(0))'
src/VaultFactory.sol, 151, b'        if(_weth == address(0))'
src/VaultFactory.sol, 154, b'        if(_treasury == address(0))'
src/VaultFactory.sol, 192, b'        if(controller == address(0))'
src/VaultFactory.sol, 201, b'            string(abi.encodePacked(_name,"HEDGE")),'
src/VaultFactory.sol, 211, b'            string(abi.encodePacked(_name,"RISK")),'
src/VaultFactory.sol, 221, b'        if (tokenToOracle[_token] == address(0)) {'
src/VaultFactory.sol, 234, b'        MarketVault memory marketVault = MarketVault(marketIndex, epochBegin, epochEnd, hedge, risk, _withdrawalFee);'
src/VaultFactory.sol, 254, b'        if(controller == address(0))'
src/VaultFactory.sol, 263, b'        MarketVault memory marketVault = MarketVault(index, epochBegin, epochEnd, Vault(hedge), Vault(risk), _withdrawalFee);'
src/VaultFactory.sol, 296, b'        if(_controller == address(0))'
src/VaultFactory.sol, 349, b'        if(_controller == address(0))'
src/VaultFactory.sol, 367, b'        if(_oracle == address(0))'
src/VaultFactory.sol, 369, b'        if(_token == address(0))'

### G09: PREFIX INCREMENT SAVE MORE GAS
#### problem
prefix increment ++i is more cheaper than postfix i++
#### prof
src/Vault.sol, 443, b'        for (uint256 i = 0; i < epochsLength(); i++) {'

### G10: USING BOOLS FOR STORAGE INCURS OVERHEAD
#### problem
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
#### prof
src/Vault.sol, 52, b'    mapping(uint256 => bool) public idDepegged;'
src/Vault.sol, 54, b'    mapping(uint256 => bool) public idExists;'

### G11: resign the default value to the variables.
#### problem
 resign the default value to the variables will cost more gas.
#### prof
src/Vault.sol, 443, b'        for (uint256 i = 0; i < epochsLength(); i++) {'
risk find_all_self_increment_with_unckeck
src/Vault.sol, 443, b'        for (uint256 i = 0; i < epochsLength(); i++) {'