### Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead or check the return value.

Bad:
```solidity
IERC20(token).transferFrom(msg.sender, address(this), amount);
```
Good (using OpenZeppelin's SafeERC20):
```solidity
import {SafeERC20} from "openzeppelin/token/utils/SafeERC20.sol";

// ...

IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
```
Good (using require):
```solidity
bool success = IERC20(token).transferFrom(msg.sender, address(this), amount);
require(success, "ERC20 transfer failed");
```

Consider using safeTransfer/safeTransferFrom or wrap in a require statement here:


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L167
```solidity
File: /src/Vault.sol
167:        asset.transferFrom(msg.sender, address(this), shares);
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L228
```solidity
File: /src/Vault.sol
228:        asset.transfer(treasury, feeValue);

231:        asset.transfer(receiver, entitledShares);

365:        asset.transfer(_counterparty, idFinalTVL[id]);

```

### Missing checks for address(0x0) when assigning values to address state variables

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L68-L70
```solidity
68:        admin = _admin;
69:        govToken = _govToken;
70:        factory = _factory;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L81-L83
```solidity
File: /src/rewards/StakingRewards.sol
81:        rewardsToken = ERC20(_rewardsToken);
82:        stakingToken = IERC1155(_stakingToken);
83:        rewardsDistribution = _rewardsDistribution;
```

### Using safeMath is no longer required with version 0.8
SafeMath is no longer needed starting with Solidity 0.8. The compiler now has built in overflow checking.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L4
```solidity
File: /src/rewards/StakingRewards.sol
4:     import {SafeMath} from "@openzeppelin/contracts/utils/math/SafeMath.sol";

29:    using SafeMath for uint256;

```
### Constants should be defined rather than using magic numbers
There are several occurrences of literal values with unexplained meaning .Literal values in the codebase without an explained meaning make the code harder to read, understand and maintain, thus hindering the experience of developers, auditors and external contributors alike.

Developers should define a constant variable for every magic value used , giving it a clear and self-explanatory name.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L177
```solidity
File: /src/rewards/StakingRewards.sol


//@audit: 1e18
168:                    .mul(1e18)
//@audit: 1e18
177:                .div(1e18)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L266
```solidity
File: /src/Vault.sol

//@audit: 1000
266:        return (amount * epochFee[_epoch]) / 1000;

//@audit: 150
311:        if(_withdrawalFee > 150)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L68
```solidity
File: /src/oracles/PegOracle.sol

//@audit: 10000
68:            nowPrice = (price2 * 10000) / price1;

//@audit: 10000
70:            nowPrice = (price1 * 10000) / price2;

//@audit: 1000000
78:            nowPrice / 1000000,

```
## abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). "Unless there is a compelling reason, abi.encode should be preferred". If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

If all arguments are strings and or bytes, bytes.concat() should be used instead

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L125-L130
```solidity
File: /src/rewards/RewardsFactory.sol
125:            keccak256(
126:                abi.encodePacked(
127:                    _marketIndex,
128:                    Vault(_insrToken).idEpochBegin(_epochEnd),
129:                    _epochEnd
130:                )
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L179-L185
```solidity
File: /src/Controller.sol
179:            keccak256(
180:                abi.encodePacked(
181:                    marketIndex,
182:                    insrVault.idEpochBegin(epochEnd),
183:                    epochEnd
184:                )
185:            ),


235:            keccak256(
236:                abi.encodePacked(
237:                    marketIndex,
238:                    insrVault.idEpochBegin(epochEnd),
239:                    epochEnd
240:                )
241:            ),
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L278
```solidity
File: /src/VaultFactory.sol
278:            keccak256(abi.encodePacked(_marketVault.index, _marketVault.epochBegin, _marketVault.epochEnd)),
```
## Prevent accidentally burning tokens

Transferring tokens to the zero address is usually prohibited to accidentally avoid "burning" tokens by sending them to an unrecoverable zero address.

Consider adding a check to prevent accidentally burning tokens here:

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L127
```solidity
File: /src/SemiFungibleVault.sol
127:        asset.safeTransfer(receiver, assets);
```

### Public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L145-L152
```solidity
File: /src/rewards/RewardsFactory.sol
145:    function getHashedIndex(uint256 _index, uint256 _epoch)
146:        public
147:        pure
148:        returns (bytes32 hashedIndex)
149:    {
150:        return keccak256(abi.encode(_index, _epoch));
151:    }
152: }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L178-L186
```solidity
File: /src/VaultFactory.sol
178:    function createNewMarket(
179:        uint256 _withdrawalFee,
180:        address _token,
181:        int256 _strikePrice,
182:       uint256 epochBegin,
183:        uint256 epochEnd,
184:        address _oracle,
185:        string memory _name
186:    ) public onlyAdmin returns (address insr, address rsk) {
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L248-L253
```solidity
File: /src/VaultFactory.sol
248:    function deployMoreAssets(
249:        uint256 index,
250:        uint256 epochBegin,
251:        uint256 epochEnd,
252:        uint256 _withdrawalFee
253:    ) public onlyAdmin {


295:    function setController(address _controller) public onlyAdmin {

366:    function changeOracle(address _token, address _oracle) public onlyAdmin {
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L148-L151
```solidity
File: /src/Controller.sol
148:    function triggerDepeg(uint256 marketIndex, uint256 epochEnd)
149:        public
150:        isDisaster(marketIndex, epochEnd)
151:    {


198:    function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
```

### Unused named return
Using both named returns and a return statement isn’t necessary in  a function.To  improve code quality, consider using only one of those.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L83-L138
```solidity
File: /src/rewards/RewardsFactory.sol
83:    function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate)
84:        external
85:        onlyAdmin
86:        returns (address insr, address risk)
87:    {

137:        return (address(insrStake), address(riskStake));
138:    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L145-L152
```solidity
File: /src/rewards/RewardsFactory.sol

//@audit: hashedIndex not used
145:    function getHashedIndex(uint256 _index, uint256 _epoch)
146:        public
147:        pure
148:        returns (bytes32 hashedIndex)
149:    {
150:        return keccak256(abi.encode(_index, _epoch));
151:    }
152: }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L261-L312
```solidity
File: /src/Controller.sol
261:    function getLatestPrice(address _token)
262:        public
263:        view
264:        returns (int256 nowPrice)
265:    {
	       ...
311:        return price;
312:    }
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L182-L193
```solidity
File: /src/Vault.sol
182:    function depositETH(uint256 id, address receiver)
183:        external
184:        payable
185:        returns (uint256 shares)
186:    {
 
192:        return deposit(id, msg.value, receiver);
193:    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L203-L234
```solidity
File: /src/Vault.sol
203:    function withdraw(
204:        uint256 id,
205:        uint256 assets,
206:        address receiver,
207:        address owner
208:    )
209:        external
210:        override
211:        epochHasEnded(id)
212:        marketExists(id)
213:        returns (uint256 shares)
214:    {


233:        return entitledShares;
234:    }
```

Named `shares` returned `entitledShares`

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L438-L451
```solidity
File: /src/Vault.sol
438:    function getNextEpoch(uint256 _epoch)
439:        public
440:        view
441:        returns (uint256 nextEpochEnd)
442:    {
443:        for (uint256 i = 0; i < epochsLength(); i++) {
444:            if (epochs[i] == _epoch) {
445:                if (i == epochsLength() - 1) {
446:                    return 0;
447:                }
448:                return epochs[i + 1];
449:            }
450:        }
451:    }
```
Named ```nextEpochEnd``` which is never mentioned anywhere in the function

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L260-L267
```solidity
File: /src/Vault.sol
260:    function calculateWithdrawalFeeValue(uint256 amount, uint256 _epoch)
261:       public
262:        view
263:        returns (uint256 feeValue)
264:    {
265:        // 0.5% = multiply by 1000 then divide by 5
266:        return (amount * epochFee[_epoch]) / 1000;
267:    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L89-L106
```solidity
File: /src/oracles/PegOracle.sol

89:    function getOracle1_Price() public view returns (int256 price) {

105:        return price1;
106:    }

112:    function getOracle2_Price() public view returns (int256 price) {

128:        return price2;
129:    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L178-L186
```solidity
File: /src/VaultFactory.sol
178:    function createNewMarket(
         ...
186:    ) public onlyAdmin returns (address insr, address rsk) {
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L385-L391
```solidity
File: /src/VaultFactory.sol
385:    function getVaults(uint256 index)
386:        public
387:        view
388:        returns (address[] memory vaults)
389:    {
390:        return indexVaults[index];
391:    }
```

### Using a return statement while also having a named return statement is redundant
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L152-L174
```solidity
File: /src/Vault.sol
152:    function deposit(
153:        uint256 id,
154:        uint256 assets,
155:        address receiver
156:    )
157:        public
158:        override
159:        marketExists(id)
160:        epochHasNotStarted(id)
161:        nonReentrant
162:        returns (uint256 shares)
163:    {
164:				// Check for rounding error since we round down in previewDeposit.
165:        require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");



173:        return shares;
174:    }
```

Consider removing one , preferebly the named return
### Unused Modifier
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L73-L77
```solidity
File: /src/Controller.sol
73:    modifier onlyAdmin() {
74:        if(msg.sender != admin)
75:            revert AddressNotAdmin();
76:        _;
77:    }
```
### Typos/Grammer
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L198
```solidity
File: /src/Vault.sol
//@audit: entitle instead of entitled
198:    @param assets   uint256 of how many assets you want to withdraw, this value will be used to calculate how many assets you are entitle to according to the events;
```

```diff

-    @param assets   uint256 of how many assets you want to withdraw, this value will be used to calculate how many assets you are entitle to according to the events;
+    @param assets   uint256 of how many assets you want to withdraw, this value will be used to calculate how many assets you are entitled to according to the events;
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L85
```solidity
File: /src/oracles/PegOracle.sol

//@audit: disbable instead of disable
85:    /* solhint-disbable-next-line func-name-mixedcase */

//@audit: disbable instead of disable
108:    /* solhint-disbable-next-line func-name-mixedcase */
```

### Open todos
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L196
```solidity
File: /src/Vault.sol
196:     @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
```

### Natspec is incomplete
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI).

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L77-L83
```solidity
File: /src/rewards/RewardsFactory.sol

//@audit: Missing @param _rewardDuration, @param _rewardRate
77:    /** @notice Trigger staking rewards event
78:      * @param _marketIndex Target market index
79:      * @param _epochEnd End of epoch set for market
80:      * @return insr Insurance rewards address, first tuple address entry 
81:      * @return risk Risk rewards address, second tuple address entry 
82:      */
83:    function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate)
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L212-L213
```solidity
File: /src/rewards/StakingRewards.sol

//@audit: Missing @param tokenAddress, @param tokenAmount
212:    // Added to support recovering LP Rewards from other systems such as BAL to be distributed to holders
213:    function recoverERC20(address tokenAddress, uint256 tokenAmount)
```

### Large multiples of ten should use scientific notation rather than decimals for readabilty/Use underscore to seperate them(Underscore demo me)
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L68
```solidity
File: /src/oracles/PegOracle.sol

//@audit: 10000
68:            nowPrice = (price2 * 10000) / price1;

//@audit: 10000
70:            nowPrice = (price1 * 10000) / price2;

//@audit: 1000000
78:            nowPrice / 1000000,

```


### Lack of event emission after critical  functions
Events help non-contract tools to track changes

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L277-L281
```solidity
File: /src/Vault.sol
277:    function changeTreasury(address _treasury) public onlyFactory {
278:        if(_treasury == address(0))
279:            revert AddressZero();
280:        treasury = _treasury;
281:    }



287:    function changeTimewindow(uint256 _timewindow) public onlyFactory {
288:        timewindow = _timewindow;
289:    }


295:    function changeController(address _controller) public onlyFactory {
296:        if(_controller == address(0))
297:            revert AddressZero();
298:        controller = _controller;
299:    }
```

### The nonReentrant modifier should occur before all other modifiers
This is a best-practice to protect against reentrancy in other modifiers

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L152-L163
```solidity
File: /src/Vault.sol
152:    function deposit(
153:        uint256 id,
154:        uint256 assets,
155:        address receiver
156:    )
157:        public
158:        override
159:        marketExists(id)
160:        epochHasNotStarted(id)
161:        nonReentrant
162:        returns (uint256 shares)
163:    {
```

###  Numeric values having something to do with time should use time units for readability
There are units for seconds , minutes ,hours,days and weeks, and since they are defined, they should be used

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L15
```solidity
File: /src/Controller.sol

//@audit: 3600
15:    uint256 private constant GRACE_PERIOD_TIME = 3600;
```

### Remove commented code
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L25
```solidity
File: /src/rewards/RewardsFactory.sol
25:    //mapping(uint => mapping(uint => address[])) public marketIndex_epoch_StakingRewards; //Market Index, Epoch, Staking Rewards [0] = insrance, [1] = risk
```

### Use string.concat() 

Solidity version 0.8.12 introduces ```string.concat(str,str) (vs abi.encodePacked(<str>,<str>))```
You can concatenate an arbitrary number of string values using string.concat. The function returns a single string memory array that contains the contents of the arguments without padding. 
Use string.concat() for the following.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L201
```solidity
File: /src/VaultFactory.sol
201:            string(abi.encodePacked(_name,"HEDGE")),

211:            string(abi.encodePacked(_name,"RISK")),
```
