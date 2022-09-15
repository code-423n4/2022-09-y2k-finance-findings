## Gas Optimizations
 *** 


### G-01: pre-increment `++i/--i` costs less gas than post-increment `i++/i--`
Saves 6 gas per loop in a for loop

Total instances of this issue: 1

instance #1
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443
```solidity
src/Vault.sol

443:        for (uint256 i = 0; i < epochsLength(); i++) {

```

 *** 


### G-02: `++i/i++` should be placed in unchecked blocks to save gas as it is impossible for them to overflow in for and while loops
Unchecked keyword is available in solidity version `0.8.0` or higher and can be applied to iterator variables to save gas. 
Saves more than `30 gas` per loop.

Total instances of this issue: 1

instance #1
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443
```solidity
src/Vault.sol

443:        for (uint256 i = 0; i < epochsLength(); i++) {

```

 *** 


### G-03: `x += y` costs more gas than `x = x + y` for state variables

Total instances of this issue: 1

instance #1
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195
```solidity
src/VaultFactory.sol

195:        marketIndex += 1;

```

 *** 


### G-04: No need to compare boolean expressions with boolean literals, directly use the expression
if (<x> == true) ==> if (<x>)
if (<x> == false) => if (!<x>)

Total instances of this issue: 6

instance #1
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L93-L94
```solidity
src/Controller.sol

93:        if(vault.idExists(epochEnd) == false)
94:            revert EpochNotExist();

```

instance #2
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L211-L212
```solidity
src/Controller.sol

211:        if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
212:            revert EpochNotExist();

```

instance #3
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L96-L97
```solidity
src/Vault.sol

96:        if((block.timestamp < id) && idDepegged[id] == false)
97:            revert EpochNotFinished();

```

instance #4
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L215-L218
```solidity
src/Vault.sol

215:        if(
216:            msg.sender != owner &&
217:            isApprovedForAll(owner, receiver) == false)
218:            revert OwnerDidNotAuthorize(msg.sender, owner);

```

instance #5
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L314-L315
```solidity
src/Vault.sol

314:        if(idExists[epochEnd] == true)
315:            revert MarketEpochExists();

```

instance #6
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L96-L97
```solidity
src/rewards/RewardsFactory.sol

96:        if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
97:            revert EpochDoesNotExist();

```

 *** 


### G-05: No need to use SafeMath if the solidity version is 0.8.0 or higher
Solidity version 0.8.0 or higher has internal overflow/underflow checks, so using SafeMath adds overhead

Total instances of this issue: 2

instance #1
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L4
```solidity
src/rewards/StakingRewards.sol

4:import {SafeMath} from "@openzeppelin/contracts/utils/math/SafeMath.sol";

```

instance #2
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L29
```solidity
src/rewards/StakingRewards.sol

29:    using SafeMath for uint256;

```

 *** 


### G-06: Adding `payable` to functions which are only meant to be called by specific actors like `onlyOwner` will save gas cost
Marking functions payable removes additional checks for whether a payment was provided, hence reducing gas cost

Total instances of this issue: 10

instance #1
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L178-L186
```solidity
src/VaultFactory.sol

178:    function createNewMarket(
179:        uint256 _withdrawalFee,
180:        address _token,
181:        int256 _strikePrice,
182:        uint256 epochBegin,
183:        uint256 epochEnd,
184:        address _oracle,
185:        string memory _name
186:    ) public onlyAdmin returns (address insr, address rsk) {

```

instance #2
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L248-L253
```solidity
src/VaultFactory.sol

248:    function deployMoreAssets(
249:        uint256 index,
250:        uint256 epochBegin,
251:        uint256 epochEnd,
252:        uint256 _withdrawalFee
253:    ) public onlyAdmin {

```

instance #3
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L295
```solidity
src/VaultFactory.sol

295:    function setController(address _controller) public onlyAdmin {

```

instance #4
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L308-L311
```solidity
src/VaultFactory.sol

308:    function changeTreasury(address _treasury, uint256 _marketIndex)
309:        public
310:        onlyAdmin
311:    {

```

instance #5
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L327-L330
```solidity
src/VaultFactory.sol

327:    function changeTimewindow(uint256 _marketIndex, uint256 _timewindow)
328:        public
329:        onlyAdmin
330:    {

```

instance #6
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L345-L348
```solidity
src/VaultFactory.sol

345:    function changeController(uint256 _marketIndex, address _controller)
346:        public
347:        onlyAdmin
348:    {

```

instance #7
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L366
```solidity
src/VaultFactory.sol

366:    function changeOracle(address _token, address _oracle) public onlyAdmin {

```

instance #8
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L213-L216
```solidity
src/rewards/StakingRewards.sol

213:    function recoverERC20(address tokenAddress, uint256 tokenAmount)
214:        external
215:        onlyOwner
216:    {

```

instance #9
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L225
```solidity
src/rewards/StakingRewards.sol

225:    function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner {

```

instance #10
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L83-L87
```solidity
src/rewards/RewardsFactory.sol

83:    function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate)
84:        external
85:        onlyAdmin
86:        returns (address insr, address risk)
87:    {

```

 *** 


### G-07: No need to initialize non-constant/non-immutable variables to zero
Since the default value is already zero, overwriting is not required.
Saves `8 gas` per instance.

Total instances of this issue: 2

instance #1
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443
```solidity
src/Vault.sol

443:        for (uint256 i = 0; i < epochsLength(); i++) {

```

instance #2
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L36
```solidity
src/rewards/StakingRewards.sol

36:    uint256 public periodFinish = 0;

```

 *** 


### G-08: Using uints/ints smaller than 256 bits increases overhead
Gas usage becomes higher with uint/int smaller than 256 bits because EVM operates on 32 bytes and uses additional operations to reduce the size from 32 bytes to the target size.

Total instances of this issue: 6

instance #1
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L291-L297
```solidity
src/Controller.sol

291:        (
292:            uint80 roundID,
293:            int256 price,
294:            ,
295:            uint256 timeStamp,
296:            uint80 answeredInRound
297:        ) = priceFeed.latestRoundData();

```

instance #2
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L13
```solidity
src/oracles/PegOracle.sol

13:    uint8 public decimals;

```

instance #3
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L46-L56
```solidity
src/oracles/PegOracle.sol

46:    function latestRoundData()
47:        public
48:        view
49:        returns (
50:            uint80 roundID,
51:            int256 nowPrice,
52:            uint256 startedAt,
53:            uint256 timeStamp,
54:            uint80 answeredInRound
55:        )
56:    {

```

instance #4
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L57-L63
```solidity
src/oracles/PegOracle.sol

57:        (
58:            uint80 roundID1,
59:            int256 price1,
60:            uint256 startedAt1,
61:            uint256 timeStamp1,
62:            uint80 answeredInRound1
63:        ) = priceFeed1.latestRoundData();

```

instance #5
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L90-L96
```solidity
src/oracles/PegOracle.sol

90:        (
91:            uint80 roundID1,
92:            int256 price1,
93:            ,
94:            uint256 timeStamp1,
95:            uint80 answeredInRound1
96:        ) = priceFeed1.latestRoundData();

```

instance #6
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L113-L119
```solidity
src/oracles/PegOracle.sol

113:        (
114:            uint80 roundID2,
115:            int256 price2,
116:            ,
117:            uint256 timeStamp2,
118:            uint80 answeredInRound2
119:        ) = priceFeed2.latestRoundData();

```

 *** 


### G-09: Using custom errors rather than revert()/require() strings will save deployment gas
Custom errors are available from solidity version 0.8.4.

Total instances of this issue: 19

instance #1
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L23
```solidity
src/oracles/PegOracle.sol

23:        require(_oracle1 != address(0), "oracle1 cannot be the zero address");

```

instance #2
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L24
```solidity
src/oracles/PegOracle.sol

24:        require(_oracle2 != address(0), "oracle2 cannot be the zero address");

```

instance #3
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L25
```solidity
src/oracles/PegOracle.sol

25:        require(_oracle1 != _oracle2, "Cannot be same Oracle");

```

instance #4
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L28-L31
```solidity
src/oracles/PegOracle.sol

28:        require(
29:            (priceFeed1.decimals() == priceFeed2.decimals()),
30:            "Decimals must be the same"
31:        );

```

instance #5
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L98
```solidity
src/oracles/PegOracle.sol

98:        require(price1 > 0, "Chainlink price <= 0");

```

instance #6
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L99-L102
```solidity
src/oracles/PegOracle.sol

99:        require(
100:            answeredInRound1 >= roundID1,
101:            "RoundID from Oracle is outdated!"
102:        );

```

instance #7
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L103
```solidity
src/oracles/PegOracle.sol

103:        require(timeStamp1 != 0, "Timestamp == 0 !");

```

instance #8
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L121
```solidity
src/oracles/PegOracle.sol

121:        require(price2 > 0, "Chainlink price <= 0");

```

instance #9
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L122-L125
```solidity
src/oracles/PegOracle.sol

122:        require(
123:            answeredInRound2 >= roundID2,
124:            "RoundID from Oracle is outdated!"
125:        );

```

instance #10
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L126
```solidity
src/oracles/PegOracle.sol

126:        require(timeStamp2 != 0, "Timestamp == 0 !");

```

instance #11
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L91
```solidity
src/SemiFungibleVault.sol

91:        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");

```

instance #12
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L116-L119
```solidity
src/SemiFungibleVault.sol

116:        require(
117:            msg.sender == owner || isApprovedForAll(owner, receiver),
118:            "Only owner can withdraw, or owner has approved receiver for all"
119:        );

```

instance #13
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L165
```solidity
src/Vault.sol

165:        require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");

```

instance #14
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187
```solidity
src/Vault.sol

187:        require(msg.value > 0, "ZeroValue");

```

instance #15
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L96
```solidity
src/rewards/StakingRewards.sol

96:        require(amount != 0, "Cannot stake 0");

```

instance #16
Link: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119
```solidity
src/rewards/StakingRewards.sol

119:        require(amount > 0, "Cannot withdraw 0");

```

instance #17
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L202-L205
```solidity
src/rewards/StakingRewards.sol

202:        require(
203:            rewardRate <= balance.div(rewardsDuration),
204:            "Provided reward too high"
205:        );

```

instance #18
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L217-L220
```solidity
src/rewards/StakingRewards.sol

217:        require(
218:            tokenAddress != address(stakingToken),
219:            "Cannot withdraw the staking token"
220:        );

```

instance #19
Permalink: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L226-L229
```solidity
src/rewards/StakingRewards.sol

226:        require(
227:            block.timestamp > periodFinish,
228:            "Previous rewards period must be complete before changing the duration for the new period"
229:        );

```

