## [G001] Don't Initialize Variables with Default Value

#### Instances: `1` found
```
2022-09-y2k-finance/src/Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```

## [G002] Use != 0 instead of > 0 for Unsigned Integer Comparison

`!= 0` costs less gas compared to `> 0` for unsigned integers in require statements with the optimizer enabled (6 gas)\For uints the minimum value would be 0 and never a negative value. Since it cannot be a negative value, then the check `> 0` is essentially checking that the value is not equal to 0 therefore `>0` can be replaced with `!=0` which saves gas.

#### Instances: `4` found
```
2022-09-y2k-finance/src/oracles/PegOracle.sol::98 => require(price1 > 0, "Chainlink price <= 0");
2022-09-y2k-finance/src/oracles/PegOracle.sol::121 => require(price2 > 0, "Chainlink price <= 0");
2022-09-y2k-finance/src/rewards/StakingRewards.sol::119 => require(amount > 0, "Cannot withdraw 0");
2022-09-y2k-finance/src/rewards/StakingRewards.sol::134 => if (reward > 0) {
```
## [G003] >= costs less gas than >

he compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas

#### Instances: `2` found
```
2022-09-y2k-finance/src/Vault.sol::311 => if(_withdrawalFee > 150)
2022-09-y2k-finance/src/rewards/StakingRewards.sol::134 => if (reward > 0) {
```

## [G004] `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

#### Instances: `1` found
```
2022-09-y2k-finance/src/VaultFactory.sol::195 => marketIndex += 1;
```

## [G005] `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### Instances: `1` found
```
2022-09-y2k-finance/src/Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```

## [G006] Using `bools` for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.Refer https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 . Use uint256(1) and uint256(2) for true/false

#### Instances: `2` found
```
2022-09-y2k-finance/src/Vault.sol::52 => mapping(uint256 => bool) public idDepegged;
2022-09-y2k-finance/src/Vault.sol::54 => mapping(uint256 => bool) public idExists;
```
## [G006] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement

This change saves 6 gas per instance

#### Instances: `1` found
```
2022-09-y2k-finance/src/rewards/StakingRewards.sol::119 => require(amount > 0, "Cannot withdraw 0");
```
## [G007] ++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)

Saves 6 gas PER LOOP

#### Instances: `1` found
```
2022-09-y2k-finance/src/Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```

## [G008] abi.encode() is less efficient than abi.encodePacked()

#### Instances: `2` found
```
2022-09-y2k-finance/src/rewards/RewardsFactory.sol::118 => bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));
2022-09-y2k-finance/src/rewards/RewardsFactory.sol::150 => return keccak256(abi.encode(_index, _epoch));
```
## [G009] Don't compare boolean expressions to boolean literals

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

#### Instances: `6` found
```
2022-09-y2k-finance/src/Controller.sol::93 => if(vault.idExists(epochEnd) == false)
2022-09-y2k-finance/src/Controller.sol::211 => if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
2022-09-y2k-finance/src/Vault.sol::96 => if((block.timestamp < id) && idDepegged[id] == false)
2022-09-y2k-finance/src/Vault.sol::217 => isApprovedForAll(owner, receiver) == false)
2022-09-y2k-finance/src/Vault.sol::314 => if(idExists[epochEnd] == true)
2022-09-y2k-finance/src/rewards/RewardsFactory.sol::96 => if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
```
## [G010] Don't use SafeMath once the solidity version is 0.8.0 or greater

Version 0.8.0 introduces internal overflow checks, so using SafeMath is redundant and adds overhead

#### Instances using `0.8.15`: `2` found
```
2022-09-y2k-finance/src/rewards/StakingRewards.sol::4 => import {SafeMath} from "@openzeppelin/contracts/utils/math/SafeMath.sol";
2022-09-y2k-finance/src/rewards/StakingRewards.sol::29 => using SafeMath for uint256;
```

## [G011] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

#### Instances: `9` found
```
2022-09-y2k-finance/src/Vault.sol::277 => function changeTreasury(address _treasury) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::287 => function changeTimewindow(uint256 _timewindow) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::295 => function changeController(address _controller) public onlyFactory {
2022-09-y2k-finance/src/Vault.sol::350 => function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
2022-09-y2k-finance/src/VaultFactory.sol::186 => ) public onlyAdmin returns (address insr, address rsk) {
2022-09-y2k-finance/src/VaultFactory.sol::253 => ) public onlyAdmin {
2022-09-y2k-finance/src/VaultFactory.sol::295 => function setController(address _controller) public onlyAdmin {
2022-09-y2k-finance/src/VaultFactory.sol::366 => function changeOracle(address _token, address _oracle) public onlyAdmin {
2022-09-y2k-finance/src/rewards/StakingRewards.sol::225 => function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner {
```
## [G012] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version `0.8.4`. The instances below match or exceed that version `0.8.15`

#### Instances: `12` found
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

## [G013] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Use a larger size then downcast where needed

#### Instances: `3` found
File: [oracles/PegOracle.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol)
```
13    uint8 public decimals;
50    uint80 roundID,
54    uint80 answeredInRound
```

## [G014] Variable Packing

solidity contracts have contiguous 32 byte (256 bit) slots used for storage. When we arrange variables so multiple fit in a single slot, it is called variable packing.
Variable packing is like a game of Tetris. If a variable we are trying to pack exceeds the 32 byte limit of the current slot, it gets stored in a new one. We must figure out which variables fit together the best to minimize wasted space.

Because each storage slot costs gas, variable packing helps us optimize our gas usage by reducing the number of slots our contract requires.

#### Instances: `10` found

File: [VaultFactory.sol ](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol)
```
20	uint256 index;
	uint256 epochBegin;
	uint256 epochEnd;
	Vault hedge;
	Vault risk;
	uint256 withdrawalFee;
```
```
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
```
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

File: [oracles/PegOracle.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol)
```
50          uint80 roundID,
            int256 nowPrice,
            uint256 startedAt,
            uint256 timeStamp,
            uint80 answeredInRound
        )
```
```
58          uint80 roundID1,
            int256 price1,
            uint256 startedAt1,
            uint256 timeStamp1,
            uint80 answeredInRound1
```
```
91          uint80 roundID1,
            int256 price1,
            ,
            uint256 timeStamp1,
            uint80 answeredInRound1
```
```
114         uint80 roundID2,
            int256 price2,
            ,
            uint256 timeStamp2,
            uint80 answeredInRound2
```

File: [rewards/StakingRewards.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol)
```
36  uint256 public periodFinish = 0;
    uint256 public rewardRate;
    uint256 public rewardsDuration;
    uint256 public lastUpdateTime;
    uint256 public rewardPerTokenStored;
    uint256 public id;


43  mapping(address => uint256) public userRewardPerTokenPaid;
    mapping(address => uint256) public rewards;


    uint256 private _totalSupply;
    mapping(address => uint256) private _balances;
```

File: [Controller.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol)
```
292         uint80 roundID,
            int256 price,
            ,
            uint256 timeStamp,
            uint80 answeredInRound
```

## [G015] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

#### Instances: `2` found

File: [VaultFactory.sol ](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol)
```
    mapping(uint256 => address[]) public indexVaults; //[0] hedge and [1] risk vault
    mapping(uint256 => uint256[]) public indexEpochs; //all epochs in the market
    mapping(address => address) public tokenToOracle; //token address to respective oracle smart contract address
```

File: [rewards/StakingRewards.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol)
```
43  mapping(address => uint256) public userRewardPerTokenPaid;
    mapping(address => uint256) public rewards;


    uint256 private _totalSupply;
    mapping(address => uint256) private _balances;
```

## [G016] Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read.

Instead of declaring the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack
variables, will be much cheaper, only incuring the Gcoldsload for the
fields actually read. The only time it makes sense to read the whole
struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct


#### Instances: `5` found
File: [VaultFactory.sol ](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol)
```
313        address[] memory vaults = indexVaults[_marketIndex];
352        address[] memory vaults = indexVaults[_marketIndex];
```

File: [Controller.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol)
```
84         address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
152        address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
206        address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);

```

## [G017] Use constants instead of type(uint232).max

#### Instances: `2` found
File: [SemiFungibleVault.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol)
```
238        return type(uint256).max;
245        return type(uint256).max;

```

## [G018] Use assembly to check for address(0)

Use assembly to check for address(0)\Saves 6 gas per instance if using assembly to check for address(0)\
			assembly {\
			 if iszero(_addr) {\
			  mstore(0x00, "zero address")\
			  revert(0x00, 0x20)\
			 }\
			}

#### Instances: `23` found
```
2022-09-y2k-finance/src/Controller.sol::126 => if(_admin == address(0))
2022-09-y2k-finance/src/Controller.sol::129 => if(_factory == address(0))
2022-09-y2k-finance/src/Controller.sol::132 => if(_l2Sequencer == address(0))
2022-09-y2k-finance/src/Vault.sol::124 => if(_treasury == address(0))
2022-09-y2k-finance/src/Vault.sol::127 => if(_controller == address(0))
2022-09-y2k-finance/src/Vault.sol::130 => if(_token == address(0))
2022-09-y2k-finance/src/Vault.sol::278 => if(_treasury == address(0))
2022-09-y2k-finance/src/Vault.sol::296 => if(_controller == address(0))
2022-09-y2k-finance/src/VaultFactory.sol::149 => if(_admin == address(0))
2022-09-y2k-finance/src/VaultFactory.sol::151 => if(_weth == address(0))
2022-09-y2k-finance/src/VaultFactory.sol::154 => if(_treasury == address(0))
2022-09-y2k-finance/src/VaultFactory.sol::192 => if(controller == address(0))
2022-09-y2k-finance/src/VaultFactory.sol::221 => if (tokenToOracle[_token] == address(0)) {
2022-09-y2k-finance/src/VaultFactory.sol::254 => if(controller == address(0))
2022-09-y2k-finance/src/VaultFactory.sol::296 => if(_controller == address(0))
2022-09-y2k-finance/src/VaultFactory.sol::349 => if(_controller == address(0))
2022-09-y2k-finance/src/VaultFactory.sol::367 => if(_oracle == address(0))
2022-09-y2k-finance/src/VaultFactory.sol::369 => if(_token == address(0))
2022-09-y2k-finance/src/oracles/PegOracle.sol::23 => require(_oracle1 != address(0), "oracle1 cannot be the zero address");
2022-09-y2k-finance/src/oracles/PegOracle.sol::24 => require(_oracle2 != address(0), "oracle2 cannot be the zero address");
2022-09-y2k-finance/src/rewards/RewardsFactory.sol::93 => if(_insrToken == address(0) || _riskToken == address(0))
2022-09-y2k-finance/src/rewards/StakingRewards.sol::63 => if (account != address(0)) {
2022-09-y2k-finance/src/rewards/StakingRewards.sol::187 => updateReward(address(0))
```