## (1) Abi.encode() Is Less Efficient Than Abi.encodepacked()

Severity: Gas Optimizations

## Proof Of Concept


	bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L118

	return keccak256(abi.encode(_index, _epoch));
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L150


## (2) Require() Should Be Used Instead Of Assert()

Severity: Gas Optimizations

## Proof Of Concept

    assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L190


## (3) Empty Blocks Should Be Removed Or Emit Something

Severity: Gas Optimizations

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

## Proof Of Concept

	) internal virtual {}
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L280

	) internal virtual {}
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L286







## (4) ++i Costs Less Gas Than i++, Especially When It’s Used In For-loops (--i/i-- Too)

Severity: Gas Optimizations

Saves 6 gas per loop

## Proof Of Concept


	for (uint256 i = 0; i < epochsLength(); i++) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L443



## Recommended Mitigation Steps

For example, use ++i instead of i++



## (5) It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied

Severity: Gas Optimizations

## Proof Of Concept


	for (uint256 i = 0; i < epochsLength(); i++) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L443






## (6) Using > 0 Costs More Gas Than != 0 When Used On A Uint In A require() Statement

Severity: Gas Optimizations

This change saves 6 gas per instance

## Proof Of Concept


	require(msg.value > 0, "ZeroValue");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L187

	require(price1 > 0, "Chainlink price <= 0");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L98

	require(price2 > 0, "Chainlink price <= 0");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L121

	require(amount > 0, "Cannot withdraw 0");
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L119





## (7) Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate

Severity: Gas Optimizations

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

## Proof Of Concept


	mapping(address => uint256) public userRewardPerTokenPaid;
    mapping(address => uint256) public rewards;
    mapping(address => uint256) private _balances;

https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L43






## (8) Not Using The Named Return Variables When A Function Returns, Wastes Deployment Gas

Severity: Gas Optimizations

## Proof Of Concept


	function getLatestPrice(address _token)
        public
        view
        returns (int256 nowPrice)
    {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L261

	function depositETH(uint256 id, address receiver)
        external
        payable
        returns (uint256 shares)
    {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L182

	function calculateWithdrawalFeeValue(uint256 amount, uint256 _epoch)
        public
        view
        returns (uint256 feeValue)
    {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L260

	function getNextEpoch(uint256 _epoch)
        public
        view
        returns (uint256 nextEpochEnd)
    {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L438

	function createNewMarket(
        uint256 _withdrawalFee,
        address _token,
        int256 _strikePrice,
        uint256 epochBegin,
        uint256 epochEnd,
        address _oracle,
        string memory _name
    ) public onlyAdmin returns (address insr, address rsk) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L178

	function getVaults(uint256 index)
        public
        view
        returns (address[] memory vaults)
    {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L385

	function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate)
        external
        onlyAdmin
        returns (address insr, address risk)
    {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L83

	function getHashedIndex(uint256 _index, uint256 _epoch)
        public
        pure
        returns (bytes32 hashedIndex)
    {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L145

    function getOracle1_Price() public view returns (int256 price) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L105

    function getOracle2_Price() public view returns (int256 price) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L128

## (9) <x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables

Severity: Gas Optimizations

## Proof Of Concept


	marketIndex += 1;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L195





## (10) Using Private Rather Than Public For Constants, Saves Gas

Severity: Gas Optimizations

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

## Proof Of Concept



    uint256 public constant VAULTS_LENGTH = 2;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L16



## Recommended Mitigation Steps

Set variable to private.



## (11) Public Functions To External

Severity: Gas Optimizations

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

## Proof Of Concept


	function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L198

	function maxDeposit(address) public view virtual returns (uint256) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L237

	function maxMint(address) public view virtual returns (uint256) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L244

	function changeTreasury(address _treasury) public onlyFactory {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L277

	function changeTimewindow(uint256 _timewindow) public onlyFactory {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L287

	function changeController(address _controller) public onlyFactory {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L295

	function createNewMarket(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L178

	function deployMoreAssets(
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L248

	function setController(address _controller) public onlyAdmin {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L295

	function changeOracle(address _token, address _oracle) public onlyAdmin {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L366

	function getOracle1_Price() public view returns (int256 price) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L89





## (12) Use of non-uint64 state variable is less efficient than uint256

Severity: Gas Optimizations

Lower than uint256 size storage variables are less gas efficient.
Using uint64 does not give any efficiency, actually, it is the opposite as EVM operates on default of 256-bit values so uint64 is more expensive in this case as it needs a conversion. It only gives improvements in cases where you can pack variables together, e.g. structs.

## Proof Of Concept


	uint8 public decimals;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L13






## (13) ++i/i++ Should Be Unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

Severity: Gas Optimizations

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

## Proof Of Concept


	for (uint256 i = 0; i < epochsLength(); i++) {
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L443





## (14) Use assembly to check for address(0)

Severity: Gas Optimizations

Save 6 gas per instance if using assembly to check for address(0)

e.g.
assembly {
	if iszero(_addr) {
		mstore(0x00, "AddressZero")
		revert(0x00, 0x20)
	}
}

## Proof Of Concept


	if(_treasury == address(0))
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L278

	if(_controller == address(0))
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L296

	if(_controller == address(0))
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L296

	if(_oracle == address(0))
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L367

	if(_token == address(0))
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L369






## (15) Using Bools For Storage Incurs Overhead

Severity: Gas Optimizations

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

## Proof Of Concept


    mapping(uint256 => bool) public idDepegged;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L52

    mapping(uint256 => bool) public idExists;
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L54





