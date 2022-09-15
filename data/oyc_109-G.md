## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2022-09-y2k-finance/src/Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```

## [G-02] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance

```
2022-09-y2k-finance/src/Vault.sol::187 => require(msg.value > 0, "ZeroValue");
2022-09-y2k-finance/src/oracles/PegOracle.sol::98 => require(price1 > 0, "Chainlink price <= 0");
2022-09-y2k-finance/src/oracles/PegOracle.sol::121 => require(price2 > 0, "Chainlink price <= 0");
2022-09-y2k-finance/src/rewards/StakingRewards.sol::119 => require(amount > 0, "Cannot withdraw 0");
```

## [G-03] Long Revert Strings

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

```
2022-09-y2k-finance/src/oracles/PegOracle.sol::23 => require(_oracle1 != address(0), "oracle1 cannot be the zero address");
2022-09-y2k-finance/src/oracles/PegOracle.sol::24 => require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```

## [G-04] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2022-09-y2k-finance/src/Controller.sol::11 => address public immutable admin;
2022-09-y2k-finance/src/Controller.sol::12 => VaultFactory public immutable vaultFactory;
2022-09-y2k-finance/src/SemiFungibleVault.sol::19 => ERC20 public immutable asset;
2022-09-y2k-finance/src/Vault.sol::34 => address public immutable tokenInsured;
2022-09-y2k-finance/src/Vault.sol::36 => int256 public immutable strikePrice;
2022-09-y2k-finance/src/VaultFactory.sol::12 => address public immutable Admin;
2022-09-y2k-finance/src/VaultFactory.sol::13 => address public immutable WETH;
2022-09-y2k-finance/src/rewards/StakingRewards.sol::34 => ERC20 public immutable rewardsToken;
2022-09-y2k-finance/src/rewards/StakingRewards.sol::35 => IERC1155 public immutable stakingToken;
```

## [G-05] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2022-09-y2k-finance/src/Vault.sol::277 => function changeTreasury(address _treasury) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::287 => function changeTimewindow(uint256 _timewindow) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::295 => function changeController(address _controller) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::350 => function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
2022-09-y2k-finance/src/VaultFactory.sol::295 => function setController(address _controller) public onlyAdmin {
2022-09-y2k-finance/src/VaultFactory.sol::366 => function changeOracle(address _token, address _oracle) public onlyAdmin {
2022-09-y2k-finance/src/rewards/StakingRewards.sol::225 => function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner {
```

## [G-06] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

```
2022-09-y2k-finance/src/SemiFungibleVault.sol::280 => ) internal virtual {}
2022-09-y2k-finance/src/SemiFungibleVault.sol::286 => ) internal virtual {}
```

## [G-07] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2022-09-y2k-finance/src/oracles/PegOracle.sol::13 => uint8 public decimals;
```

## [G-08] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2022-09-y2k-finance/src/Vault.sol::52 => mapping(uint256 => bool) public idDepegged;
2022-09-y2k-finance/src/Vault.sol::54 => mapping(uint256 => bool) public idExists;
```

## [G-09] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-09-y2k-finance/src/Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```

## [G-10] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2022-09-y2k-finance/src/VaultFactory.sol::195 => marketIndex += 1;
```

## [G-11] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

```
2022-09-y2k-finance/src/rewards/RewardsFactory.sol::118 => bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));
2022-09-y2k-finance/src/rewards/RewardsFactory.sol::150 => return keccak256(abi.encode(_index, _epoch));
```

## [G-12] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2022-09-y2k-finance/src/SemiFungibleVault.sol::91 => require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
2022-09-y2k-finance/src/Vault.sol::165 => require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
2022-09-y2k-finance/src/Vault.sol::187 => require(msg.value > 0, "ZeroValue");
2022-09-y2k-finance/src/oracles/PegOracle.sol::23 => require(_oracle1 != address(0), "oracle1 cannot be the zero address");
2022-09-y2k-finance/src/oracles/PegOracle.sol::24 => require(_oracle2 != address(0), "oracle2 cannot be the zero address");
2022-09-y2k-finance/src/oracles/PegOracle.sol::25 => require(_oracle1 != _oracle2, "Cannot be same Oracle");
2022-09-y2k-finance/src/oracles/PegOracle.sol::98 => require(price1 > 0, "Chainlink price <= 0");
2022-09-y2k-finance/src/oracles/PegOracle.sol::103 => require(timeStamp1 != 0, "Timestamp == 0 !");
2022-09-y2k-finance/src/oracles/PegOracle.sol::121 => require(price2 > 0, "Chainlink price <= 0");
2022-09-y2k-finance/src/oracles/PegOracle.sol::126 => require(timeStamp2 != 0, "Timestamp == 0 !");
2022-09-y2k-finance/src/rewards/StakingRewards.sol::96 => require(amount != 0, "Cannot stake 0");
2022-09-y2k-finance/src/rewards/StakingRewards.sol::119 => require(amount > 0, "Cannot withdraw 0");
```

## [G-13] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
2022-09-y2k-finance/src/Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```

## [G-14] Don't compare boolean expressions to boolean literals

The extran comparison wastes gas
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

```
2022-09-y2k-finance/src/Vault.sol::314 => if(idExists[epochEnd] == true)
```

## [G-15] Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas.
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).

```
2022-09-y2k-finance/src/Vault.sol::190 => assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
```

## [G-16] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2022-09-y2k-finance/src/Controller.sol::198 => function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
2022-09-y2k-finance/src/SemiFungibleVault.sol::237 => function maxDeposit(address) public view virtual returns (uint256) {
2022-09-y2k-finance/src/SemiFungibleVault.sol::244 => function maxMint(address) public view virtual returns (uint256) {
2022-09-y2k-finance/src/Vault.sol::277 => function changeTreasury(address _treasury) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::287 => function changeTimewindow(uint256 _timewindow) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::295 => function changeController(address _controller) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::350 => function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
2022-09-y2k-finance/src/VaultFactory.sol::295 => function setController(address _controller) public onlyAdmin {
2022-09-y2k-finance/src/VaultFactory.sol::366 => function changeOracle(address _token, address _oracle) public onlyAdmin {
2022-09-y2k-finance/src/oracles/PegOracle.sol::89 => function getOracle1_Price() public view returns (int256 price) {
```

## [G-17] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2022-09-y2k-finance/src/Controller.sol::264 => returns (int256 nowPrice)
2022-09-y2k-finance/src/Vault.sol::162 => returns (uint256 shares)
2022-09-y2k-finance/src/Vault.sol::185 => returns (uint256 shares)
2022-09-y2k-finance/src/Vault.sol::213 => returns (uint256 shares)
2022-09-y2k-finance/src/Vault.sol::263 => returns (uint256 feeValue)
2022-09-y2k-finance/src/Vault.sol::381 => returns (uint256 entitledAmount)
2022-09-y2k-finance/src/Vault.sol::441 => returns (uint256 nextEpochEnd)
2022-09-y2k-finance/src/oracles/PegOracle.sol::89 => function getOracle1_Price() public view returns (int256 price) {
2022-09-y2k-finance/src/oracles/PegOracle.sol::112 => function getOracle2_Price() public view returns (int256 price) {
2022-09-y2k-finance/src/rewards/RewardsFactory.sol::148 => returns (bytes32 hashedIndex)
```

## [G-18] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2022-09-y2k-finance/src/rewards/StakingRewards.sol::43 => mapping(address => uint256) public userRewardPerTokenPaid;
2022-09-y2k-finance/src/rewards/StakingRewards.sol::44 => mapping(address => uint256) public rewards;
2022-09-y2k-finance/src/rewards/StakingRewards.sol::47 => mapping(address => uint256) private _balances;
```

## [G-19] Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

instances:

```
2022-09-y2k-finance/src/oracles/PegOracle.sol::23 => require(_oracle1 != address(0), "oracle1 cannot be the zero address");
2022-09-y2k-finance/src/oracles/PegOracle.sol::24 => require(_oracle2 != address(0), "oracle2 cannot be the zero address");
2022-09-y2k-finance/src/rewards/StakingRewards.sol::63 => if (account != address(0)) {
```

## [G-20] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```
2022-09-y2k-finance/src/VaultFactory.sol::313 => address[] memory vaults = indexVaults[_marketIndex];
2022-09-y2k-finance/src/VaultFactory.sol::331 => address[] memory vaults = indexVaults[_marketIndex];
2022-09-y2k-finance/src/VaultFactory.sol::352 => address[] memory vaults = indexVaults[_marketIndex];
```

## [G-21] Don't use SafeMath once the solidity version is 0.8.0 or greater

Version 0.8.0 introduces internal overflow checks, so using SafeMath is redundant and adds overhead

```
2022-09-y2k-finance/src/rewards/StakingRewards.sol::4 => import {SafeMath} from "@openzeppelin/contracts/utils/math/SafeMath.sol";
```

## [G-22] State variables only set in the constructor should be declared immutable

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

```
2022-09-y2k-finance/src/SemiFungibleVault.sol::71 => name = _name;
2022-09-y2k-finance/src/SemiFungibleVault.sol::72 => symbol = _symbol;
2022-09-y2k-finance/src/oracles/PegOracle.sol::36 => decimals = priceFeed1.decimals();
```