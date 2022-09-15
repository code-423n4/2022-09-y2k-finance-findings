### [G-01] ```abi.encode()``` is less efficient than ```abi.encodePacked()```


#### Impact
Consider using ```abi.encodePacked()``` instead for efficieny.


#### Findings:
```
src/rewards/RewardsFactory.sol:L118        bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));

src/rewards/RewardsFactory.sol:L150        return keccak256(abi.encode(_index, _epoch));

```
### [G-02] Use assembly to check for zero address.


#### Impact
Save 6 gas when assembly is used to check for zero address.
e.g:
```
assembly {
            if iszero(_addr) {
                mstore(0x00, "zero address")
                revert(0x00, 0x20)
            }
```


#### Findings:
```
src/oracles/PegOracle.sol:L23        require(_oracle1 != address(0), "oracle1 cannot be the zero address");

src/oracles/PegOracle.sol:L24        require(_oracle2 != address(0), "oracle2 cannot be the zero address");

```
### [G-03] Using bools for storage incurs overhead.


#### Impact
 
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use ```uint256(1)``` and ```uint256(2)``` for true/false to avoid a Gwarmaccess ([100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past


#### Findings:
```
src/Vault.sol:L52    mapping(uint256 => bool) public idDepegged;

src/Vault.sol:L54    mapping(uint256 => bool) public idExists;

```
### [G-04] Use custom errors rather than ```revert()```/```require()``` string to save gas


#### Impact
Custom errors are available from solidity version 0.8.4, it saves around 50 gas each time they are hit by avoiding having to allocate and store the revert string.


#### Findings:
```
src/SemiFungibleVault.sol:L91        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");

src/Vault.sol:L165        require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");

src/Vault.sol:L187        require(msg.value > 0, "ZeroValue");

src/rewards/StakingRewards.sol:L96        require(amount != 0, "Cannot stake 0");

src/rewards/StakingRewards.sol:L119        require(amount > 0, "Cannot withdraw 0");

src/oracles/PegOracle.sol:L23        require(_oracle1 != address(0), "oracle1 cannot be the zero address");

src/oracles/PegOracle.sol:L24        require(_oracle2 != address(0), "oracle2 cannot be the zero address");

src/oracles/PegOracle.sol:L25        require(_oracle1 != _oracle2, "Cannot be same Oracle");

src/oracles/PegOracle.sol:L98        require(price1 > 0, "Chainlink price <= 0");

src/oracles/PegOracle.sol:L103        require(timeStamp1 != 0, "Timestamp == 0 !");

src/oracles/PegOracle.sol:L121        require(price2 > 0, "Chainlink price <= 0");

src/oracles/PegOracle.sol:L126        require(timeStamp2 != 0, "Timestamp == 0 !");

```
### [G-05] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
src/SemiFungibleVault.sol:L280    ) internal virtual {}

src/SemiFungibleVault.sol:L286    ) internal virtual {}

```
### [G-06] Public functions not called by the contract should be declared external instead


#### Impact
public functions that are never called by the contract should be declared external to save gas.


#### Findings:
```
src/Controller.sol:L198    function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {

src/SemiFungibleVault.sol:L237    function maxDeposit(address) public view virtual returns (uint256) {

src/SemiFungibleVault.sol:L818    function maxMint(address) public view virtual returns (uint256) {

src/VaultFactory.sol:L295    function setController(address _controller) public onlyAdmin {

src/VaultFactory.sol:L758    function changeOracle(address _token, address _oracle) public onlyAdmin {

```
### [G-07] Using ```> 0``` costs more gas than ```!= 0``` when used on a uint in a ```require()``` statement.


#### Impact
When working with unsigned integer types, comparisons with != 0 are cheaper than with > 0 . This changes saves 6 gas per instance.


#### Findings:
```
src/Vault.sol:L187        require(msg.value > 0, "ZeroValue");

src/rewards/StakingRewards.sol:L119        require(amount > 0, "Cannot withdraw 0");

src/oracles/PegOracle.sol:L98        require(price1 > 0, "Chainlink price <= 0");

src/oracles/PegOracle.sol:L121        require(price2 > 0, "Chainlink price <= 0");

```

### [G-08] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
src/Vault.sol:L277    function changeTreasury(address _treasury) public onlyFactory {

src/Vault.sol:L287    function changeTimewindow(uint256 _timewindow) public onlyFactory {

src/Vault.sol:L295    function changeController(address _controller) public onlyFactory {

src/Vault.sol:L350    function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {

src/VaultFactory.sol:L295    function setController(address _controller) public onlyAdmin {

src/VaultFactory.sol:L366    function changeOracle(address _token, address _oracle) public onlyAdmin {

src/rewards/StakingRewards.sol:L225    function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner {

```
### [G-09] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
src/VaultFactory.sol:L195        marketIndex += 1;

```
### [G-10] ++i costs less gas than i++, especially when it's used in for loops.


#### Impact
Saves 6 gas per loop.


#### Findings:
```
src/Vault.sol:L443        for (uint256 i = 0; i < epochsLength(); i++) {

```
### [G-11] Using private rather than public for constants to saves gas.


#### Impact
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.


#### Findings:
```
src/Controller.sol:L16    uint256 public constant VAULTS_LENGTH = 2;

```
### [G-12] Revert message greater than 32 bytes


#### Impact
Keep revert message lower than or equal to 32 bytes to save gas.


#### Findings:
```
src/oracles/PegOracle.sol:L23        require(_oracle1 != address(0), "oracle1 cannot be the zero address");

src/oracles/PegOracle.sol:L24        require(_oracle2 != address(0), "oracle2 cannot be the zero address");

```
### [G-13] Explicit initialization with zero not required


#### Impact
Explicit initialization with zero is not required for variable declaration because uints are 0 by default. Removing this will reduce contract size and save a bit of gas.


#### Findings:
```
src/Vault.sol:L443        for (uint256 i = 0; i < epochsLength(); i++) {

src/rewards/StakingRewards.sol:L36    uint256 public periodFinish = 0;

```
### [G-14] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
src/Vault.sol:L443        for (uint256 i = 0; i < epochsLength(); i++) {

```
