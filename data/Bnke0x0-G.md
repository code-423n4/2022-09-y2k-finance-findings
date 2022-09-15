

### [G01] State variables only set in the constructor should be declared `immutable`


#### Findings:
```
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::20 => string public name;
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::21 => string public symbol;
2022-09-y2k-finance-main/src/Vault.sol::35 => address private treasury;
2022-09-y2k-finance-main/src/Vault.sol::38 => address public controller;
2022-09-y2k-finance-main/src/Vault.sol::40 => uint256[] public epochs;
2022-09-y2k-finance-main/src/Vault.sol::41 => uint256 public timewindow;
2022-09-y2k-finance-main/src/VaultFactory.sol::15 => address public treasury;
2022-09-y2k-finance-main/src/VaultFactory.sol::16 => address public controller;
2022-09-y2k-finance-main/src/VaultFactory.sol::17 => uint256 public marketIndex;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::10 => address public oracle1;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::11 => address public oracle2;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::13 => uint8 public decimals;
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::9 => address public admin;
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::10 => address public govToken;
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::11 => address public factory;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::37 => uint256 public rewardRate;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::38 => uint256 public rewardsDuration;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::39 => uint256 public lastUpdateTime;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::40 => uint256 public rewardPerTokenStored;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::41 => uint256 public id;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::46 => uint256 private _totalSupply;
```



### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor, avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper

#### Findings:
```
2022-09-y2k-finance-main/src/VaultFactory.sol::15 => address public treasury;
2022-09-y2k-finance-main/src/VaultFactory.sol::16 => address public controller;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::10 => address public oracle1;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::11 => address public oracle2;
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::9 => address public admin;
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::10 => address public govToken;
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::11 => address public factory;
```



### [G03] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.

#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```



### [G04] `require()`/`revert()` strings longer than 32 bytes cost extra gas



#### Findings:
```
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::23 => require(_oracle1 != address(0), "oracle1 cannot be the zero address");
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::24 => require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```



### [G05] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::144 => return _totalSupply;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::148 => return _balances[account];
```



### [G06] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement


#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::187 => require(msg.value > 0, "ZeroValue");
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::98 => require(price1 > 0, "Chainlink price <= 0");
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::121 => require(price2 > 0, "Chainlink price <= 0");
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::119 => require(amount > 0, "Cannot withdraw 0");
```



### [G07] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```



### [G08] `++i` costs less gas than `i++`, especially when it’s used in forloops (`--i`/`i--` too)




#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```


### [G09] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead


#### Findings:
```
2022-09-y2k-finance-main/src/Controller.sol::292 => uint80 roundID,
2022-09-y2k-finance-main/src/Controller.sol::296 => uint80 answeredInRound
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::13 => uint8 public decimals;
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::50 => uint80 roundID,
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::54 => uint80 answeredInRound
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::58 => uint80 roundID1,
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::62 => uint80 answeredInRound1
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::91 => uint80 roundID1,
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::95 => uint80 answeredInRound1
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::114 => uint80 roundID2,
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::118 => uint80 answeredInRound2
```



### [G10] Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

#### Impact
See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detail description of the issue

#### Findings:
```
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::118 => bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));
```



### [G11] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function


#### Findings:
```
2022-09-y2k-finance-main/src/Controller.sol::75 => revert AddressNotAdmin();
2022-09-y2k-finance-main/src/Controller.sol::88 => revert MarketDoesNotExist(marketIndex);
2022-09-y2k-finance-main/src/Controller.sol::94 => revert EpochNotExist();
2022-09-y2k-finance-main/src/Controller.sol::127 => revert ZeroAddress();
2022-09-y2k-finance-main/src/Controller.sol::218 => revert NotZeroTVL();
2022-09-y2k-finance-main/src/Vault.sol::131 => revert AddressZero();
2022-09-y2k-finance-main/src/VaultFactory.sol::255 => revert ControllerNotSet();
```



### [G12] `require()` or `revert()` statements that check input arguments should be at the top of the function

#### Impact
Checks that involve constants should come before checks that involve state variables

#### Findings:
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::23 => require(_oracle1 != address(0), "oracle1 cannot be the zero address");
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::24 => require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```



### [G13] Use custom errors rather than `revert()`/`require()` strings to save deployment gas



#### Findings:
```
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::91 => require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
2022-09-y2k-finance-main/src/Vault.sol::165 => require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
2022-09-y2k-finance-main/src/Vault.sol::187 => require(msg.value > 0, "ZeroValue");
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::23 => require(_oracle1 != address(0), "oracle1 cannot be the zero address");
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::24 => require(_oracle2 != address(0), "oracle2 cannot be the zero address");
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::25 => require(_oracle1 != _oracle2, "Cannot be same Oracle");
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::98 => require(price1 > 0, "Chainlink price <= 0");
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::103 => require(timeStamp1 != 0, "Timestamp == 0 !");
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::121 => require(price2 > 0, "Chainlink price <= 0");
2022-09-y2k-finance-main/src/oracles/PegOracle.sol::126 => require(timeStamp2 != 0, "Timestamp == 0 !");
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::94 => revert MarketDoesNotExist(_marketIndex);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::96 => require(amount != 0, "Cannot stake 0");
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::119 => require(amount > 0, "Cannot withdraw 0");
```



### [G14] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::277 => function changeTreasury(address _treasury) public onlyFactory {
2022-09-y2k-finance-main/src/Vault.sol::287 => function changeTimewindow(uint256 _timewindow) public onlyFactory {
2022-09-y2k-finance-main/src/Vault.sol::295 => function changeController(address _controller) public onlyFactory {
2022-09-y2k-finance-main/src/Vault.sol::350 => function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
2022-09-y2k-finance-main/src/VaultFactory.sol::295 => function setController(address _controller) public onlyAdmin {
2022-09-y2k-finance-main/src/VaultFactory.sol::366 => function changeOracle(address _token, address _oracle) public onlyAdmin {
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::225 => function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner {
```








### [G15] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done at some places, it’s not consistently done in the solution.

I suggest adding a non-zero-value

#### Findings:
```
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::127 => asset.safeTransfer(receiver, assets);
2022-09-y2k-finance-main/src/Vault.sol::167 => asset.transferFrom(msg.sender, address(this), shares);
2022-09-y2k-finance-main/src/Vault.sol::190 => assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
2022-09-y2k-finance-main/src/Vault.sol::228 => asset.transfer(treasury, feeValue);
2022-09-y2k-finance-main/src/Vault.sol::231 => asset.transfer(receiver, entitledShares);
2022-09-y2k-finance-main/src/Vault.sol::365 => asset.transfer(_counterparty, idFinalTVL[id]);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::136 => rewardsToken.safeTransfer(msg.sender, reward);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::221 => ERC20(tokenAddress).safeTransfer(owner, tokenAmount);
```



### [G16] Don’t use SafeMath once the solidity version is 0.8.0 or greater

#### Impact
Version 0.8.0 introduces internal overflow checks, so using SafeMath is redundant and adds overhead

#### Findings:
```
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::120 => _totalSupply = _totalSupply.sub(amount);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::121 => _balances[msg.sender] = _balances[msg.sender].sub(amount);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::152 => return rewardRate.mul(rewardsDuration);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::166 => .sub(lastUpdateTime)
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::167 => .mul(rewardRate)
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::168 => .mul(1e18)
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::176 => .mul(rewardPerToken().sub(userRewardPerTokenPaid[account]))
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::192 => uint256 remaining = periodFinish.sub(block.timestamp);
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::193 => uint256 leftover = remaining.mul(rewardRate);
```


### [G17] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

#### Impact
Saves a storage slot for the mapping. Depending on the circumstances 
and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires
 both values and they both fit in the same storage slot

#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::47 => mapping(uint256 => uint256) public idFinalTVL;
2022-09-y2k-finance-main/src/Vault.sol::48 => mapping(uint256 => uint256) public idClaimTVL;
2022-09-y2k-finance-main/src/Vault.sol::50 => mapping(uint256 => uint256) public idEpochBegin;
2022-09-y2k-finance-main/src/Vault.sol::52 => mapping(uint256 => bool) public idDepegged;
2022-09-y2k-finance-main/src/Vault.sol::54 => mapping(uint256 => bool) public idExists;
2022-09-y2k-finance-main/src/Vault.sol::55 => mapping(uint256 => uint256) public epochFee;

2022-09-y2k-finance-main/src/VaultFactory.sol::119 => mapping(uint256 => address[]) public indexVaults; //[0] hedge and [1] risk vault
2022-09-y2k-finance-main/src/VaultFactory.sol::120 => mapping(uint256 => uint256[]) public indexEpochs; //all epochs in the market

2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::43 => mapping(address => uint256) public userRewardPerTokenPaid;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::44 => mapping(address => uint256) public rewards;
2022-09-y2k-finance-main/src/rewards/StakingRewards.sol::47 => mapping(address => uint256) private _balances;
```



### [G18] Using `bools` for storage incurs overhead


#### Findings:
```
2022-09-y2k-finance-main/src/Controller.sol::52 => bool isDisaster,
2022-09-y2k-finance-main/src/Controller.sol::277 => bool isSequencerUp = answer == 0;
2022-09-y2k-finance-main/src/Vault.sol::52 => mapping(uint256 => bool) public idDepegged;
2022-09-y2k-finance-main/src/Vault.sol::54 => mapping(uint256 => bool) public idExists;
```



### [G19] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code. Savings are due to the compiler not having to create non-payable 
getter functions for deployment calldata, and not adding another entry 
to the method ID table

#### Findings:
```
2022-09-y2k-finance-main/src/Controller.sol::16 => uint256 public constant VAULTS_LENGTH = 2;
```



### [G20] Usage of `assert()` instead of `require()`

#### Impact
Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas. (see https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions).require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).
From the current usage of assert, my guess is that they can be replaced with require, unless a Panic really is intended.

#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::190 => assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
```



### [G21] Re-creation of short `memory` arrays is cheaper than fetching indecies from a storage array

#### Impact
Since the arrays are relatively short, it’s cheaper to have a pure virtual function that [re-creates them every time](https://ethereum.stackexchange.com/questions/66388/standard-work-around-for-using-a-solidity-constant-array-which-is-not-supported/96599#96599)
 just to fetch a specific index, rather than incuring a Gcoldsload (2100
 gas). Since this is in one of the two frequently-called functions, 
it’ll save a lot of gas

#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::402 => 1 ether
2022-09-y2k-finance-main/src/Vault.sol::409 => 1 ether
2022-09-y2k-finance-main/src/Vault.sol::421 => 1 ether
```






### [G22] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### Findings:
```
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::280 => ) internal virtual {}
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::286 => ) internal virtual {}
```



### [G23] Remove or replace unused state variables

#### Impact
Saves a storage slot. If the variable is assigned a non-zero value, 
saves Gsset (20000 gas). If it’s assigned a zero value, saves Gsreset 
(2900 gas). If the variable remains unassigned, there is no gas savings 
unless the variable is public, in which case the 
compiler-generated non-payable getter deployment cost is saved. If the 
state variable is overriding an interface’s public function, mark the 
variable as constant or immutable so that it does not use a storage slot

#### Findings:
```
2022-09-y2k-finance-main/src/Vault.sol::40 => uint256[] public epochs;
2022-09-y2k-finance-main/src/VaultFactory.sol::119 => mapping(uint256 => address[]) public indexVaults; //[0] hedge and [1] risk vault
2022-09-y2k-finance-main/src/VaultFactory.sol::120 => mapping(uint256 => uint256[]) public indexEpochs; //all epochs in the market
```


### [G24] `abi.encode()` is less efficient than abi.encodePacked()


#### Findings:
```
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::118 => bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));
2022-09-y2k-finance-main/src/rewards/RewardsFactory.sol::150 => return keccak256(abi.encode(_index, _epoch));
```


### [G25] Optimize names to save gas

#### Impact
public/external function names and public member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9)
 link for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

#### Findings:
```
2022-09-y2k-finance-main/src/SemiFungibleVault.sol::12 => abstract contract SemiFungibleVault is ERC1155Supply {
```

