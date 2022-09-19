## FINDINGS
NB: *Some functions have been truncated where neccessary to just show affected parts of the code*
Throught the report some places might be denoted with audit tags to show the actual place affected.

### Using immutable on variables that are only set in the constructor and never after 
Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD.
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas)

There are 12 instances as referenced below.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L9-L11
```solidity
File: /src/rewards/RewardsFactory.sol
9:    address public admin;
10:    address public govToken;
11:    address public factory;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L41
```solidity
File: /src/rewards/StakingRewards.sol
41:    uint256 public id;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L10-L16
```solidity
File: /src/oracles/PegOracle.sol
10:    address public oracle1;
11:    address public oracle2;

13:    uint8 public decimals;

15:    AggregatorV3Interface internal priceFeed1;
16:    AggregatorV3Interface internal priceFeed2;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L13
```solidity
File: /src/Controller.sol
13:    AggregatorV2V3Interface internal sequencerUptimeFeed;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L20-L21
```solidity
File: /src/SemiFungibleVault.sol
20:    string public name;
21:    string public symbol;
```

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L83-L138
### RewardsFactory.createStakingRewards(): admin should be cached(Saves 3 SLOADs)
```solidity
File: /src/rewards/RewardsFactory.sol
83:    function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate)

99:        StakingRewards insrStake = new StakingRewards(
100:            admin, //@audit: SLOAD 1
101:            admin, //@audit: SLOAD 2
102:            govToken,
103:            _insrToken,
104:            _epochEnd,
105:            _rewardDuration,
106:            _rewardRate
107:        );
108:        StakingRewards riskStake = new StakingRewards(
109:            admin, //@audit: SLOAD 3
110:            admin, //@audit: SLOAD 4
111:            govToken,
112:            _riskToken,
113:            _epochEnd,
114:            _rewardDuration,
115:            _rewardRate
 116:       );


138:    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L83-L138
### RewardsFactory.createStakingRewards(): govToken should be cached(Saves 1 SLOAD)
```solidity
File: /src/rewards/RewardsFactory.sol
83:    function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate)

102:            govToken, //@audit: SLOAD 1

107:        );
108:        StakingRewards riskStake = new StakingRewards(

111:            govToken, //@audit: SLOAD 2


138:    }
```

Note: For the above two cases, we can solve this issue by just declaring this variables as immutables


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L155-L157
### StakingRewards.sol.lastTimeRewardApplicable(): periodFinish should be cached(Saves 1 SLOAD)
```solidity
File: /src/rewards/StakingRewards.sol
155:    function lastTimeRewardApplicable() public view returns (uint256) {
156:        return block.timestamp < periodFinish ? block.timestamp : periodFinish;//@audit: periodFinish SLOAD 1 ,2
157:    }
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L183-L195
### StakingRewards.sol.notifyRewardAmount(): periodFinish should be cached(Saves 1 SLOAD)
```solidity
File: /src/rewards/StakingRewards.sol
183:    function notifyRewardAmount(uint256 reward)

188:    {
189:        if (block.timestamp >= periodFinish) { //@audit: periodFinish SLOAD 1
190:            rewardRate = reward.div(rewardsDuration);
191:        } else {
192:            uint256 remaining = periodFinish.sub(block.timestamp); //@audit: periodFinish SLOAD 2
 
195:        }
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L183-L210
### StakingRewards.sol.notifyRewardAmount(): rewardsDuration should be cached(Saves 2 SLOADs)
```solidity
File: /src/rewards/StakingRewards.sol
183:    function notifyRewardAmount(uint256 reward)
 
189:        if (block.timestamp >= periodFinish) {
190:            rewardRate = reward.div(rewardsDuration);//@audit: rewardsDuration SLOAD 1 (happy path)
191:        } else {

194:            rewardRate = reward.add(leftover).div(rewardsDuration); //@audit: rewardsDuration SLOAD 1 (Sad path)
195:        }

202:        require(
203:            rewardRate <= balance.div(rewardsDuration), //@audit: rewardsDuration SLOAD 2 
204:            "Provided reward too high"
205:        );

208:        periodFinish = block.timestamp.add(rewardsDuration); //@audit: rewardsDuration SLOAD 3

210:    }
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L90-L107
### StakingRewards.sol.stake(): id should be cached(Saves 1 SLOAD) - This can also be sorted by just declaring id as an immutable
```solidity
File: /src/rewards/StakingRewards.sol
90:    function stake(uint256 amount)
 
99:        stakingToken.safeTransferFrom(
100:            msg.sender,
101:            address(this),
102:            id, //@audit: id SLOAD 1
103:            amount,
104:            ""
105:        );
106:        emit Staked(msg.sender, id, amount); //@audit: id SLOAD 2
107:    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L114-L130
### StakingRewards.sol.withdraw(): id should be cached (Saves 1 SLOAD)- This can also be sorted by just declaring id as an immutable
```solidity
File: /src/rewards/StakingRewards.sol
114:    function withdraw(uint256 amount)
	
122:        stakingToken.safeTransferFrom(
123:            address(this),
124:            msg.sender,
125:            id, //@audit: id SLOAD 1
126:            amount,
127:            ""
128:        );
129:        emit Withdrawn(msg.sender, id, amount);//@audit: id SLOAD 2
130:    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L159-L171
### StakingRewards.sol.rewardPerToken(): \_totalSupply should be cached(Saves  1 SLOAD)
```solidity
File: /src/rewards/StakingRewards.sol
159:    function rewardPerToken() public view returns (uint256) {
160:        if (_totalSupply == 0) { //@audit: _totalSupply SLOAD 1
161:            return rewardPerTokenStored;
162:        }
163:        return
164:            rewardPerTokenStored.add(
165:                lastTimeRewardApplicable()
166:                    .sub(lastUpdateTime)
167:                    .mul(rewardRate)
168:                    .mul(1e18)
169:                    .div(_totalSupply) //@audit: _totalSupply SLOAD 2
170:            );
171:    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L191-L205
### StakingRewards.sol.notifyRewardAmount(): rewardRate should be cached(Saves 2 SLOADs)
```solidity
File: /src/rewards/StakingRewards.sol
191:        } else {
192:            uint256 remaining = periodFinish.sub(block.timestamp);
193:            uint256 leftover = remaining.mul(rewardRate); //@audit: rewardRate SLOAD 1
194:            rewardRate = reward.add(leftover).div(rewardsDuration);//@audit: rewardRate SSTORE 1
195:        }

202:        require(
203:            rewardRate <= balance.div(rewardsDuration), //@audit: rewardRate SLOAD 2
204:            "Provided reward too high"
205:        );
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L178-L239
### VaultFactory.sol.createNewMarket(): controller should be cached(Saves 3 SLOADs)
```solidity
File: /src/VaultFactory.sol
178:    function createNewMarket(
	
186:    ) public onlyAdmin returns (address insr, address rsk) {
187:        if(
188:            IController(controller).getVaultFactory() != address(this) //@audit: SLOAD 1
189:            )
190:            revert AddressFactoryNotInController();

192:        if(controller == address(0)) //@audit: SLOAD 2
193:            revert ControllerNotSet();

199:        Vault hedge = new Vault(

206:            controller //@audit: SLOAD 3
207:        );

209:        Vault risk = new Vault(

216:            controller //@audit: SLOAD 4
217:        );

238:        return (address(hedge), address(risk));
239:    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L178-L239
### VaultFactory.sol.createNewMarket(): marketIndex should be cached(Saves 3 SLOADs)
```solidity
File: /src/VaultFactory.sol
195:        marketIndex += 1; //@audit: SLOAD 1

219:        indexVaults[marketIndex] = [address(hedge), address(risk)]; //@audit: SLOAD 2

225:        emit MarketCreated(
226:            marketIndex,  //@audit: SLOAD 3
227:            address(hedge),
228:            address(risk),
229:            _token,
230:            _name,
231:            _strikePrice
232:        );

234:        MarketVault memory marketVault = MarketVault(marketIndex, epochBegin, epochEnd, hedge, risk, _withdrawalFee);//@audit: SLOAD 4

238:        return (address(hedge), address(risk));
239:    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L199-L217
### VaultFactory.sol.createNewMarket(): treasury should be cached(Saves 1 SLOAD)
```solidity
File: /src/VaultFactory.so
199:        Vault hedge = new Vault(

203:            treasury, //@audit: SLOAD 1
			
207:        );

209:        Vault risk = new Vault(
		
213:            treasury, //@audit: SLOAD 2

217:        );
```

### The result of a function call should be cached rather than re-calling the function

Consider caching the following:

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L438-L451
### Vault.sol.getNextEpoch():  the result of epochsLength()  should be cached and used instead of calling it repeatedly in the loop
```solidity
File: /src/Vault.sol
438:    function getNextEpoch(uint256 _epoch)
439:        public
440:        view
441:        returns (uint256 nextEpochEnd)
442:    {
443:        for (uint256 i = 0; i < epochsLength(); i++) { // @audit: epochsLength() called repeatedly inside the loop
444:            if (epochs[i] == _epoch) {
445:                if (i == epochsLength() - 1) { //@audit: epochsLength() called again here. 
446:                    return 0;
447:                }
448:                return epochs[i + 1];
449:            }
450:        }
451:    }
```

The ```epochsLength()``` is declared on [Line 430](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L430-L432) and is only used inside the above function to return the length of ```epochs``` . As such the whole function can even be removed and ```epochs.length``` called defined in the function `getNextEpoch`

```diff
   
diff --git a/src/Vault.sol b/src/Vault.sol
index 1d2e6df..63808b4 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -440,9 +440,10 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
         view
         returns (uint256 nextEpochEnd)
     {
-        for (uint256 i = 0; i < epochsLength(); i++) {
+        uint256 epochsLength = epochs.length;
+        for (uint256 i = 0; i < epochsLength; i++) {
             if (epochs[i] == _epoch) {
-                if (i == epochsLength() - 1) {
+                if (i == epochsLength - 1) {
                     return 0;
                 }
                 return epochs[i + 1];
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L22-L37
### PegOracle.sol.constructor():  Result of priceFeed1.decimals() should be cached
```solidity
File: /src/oracles/PegOracle.sol
22:    constructor(address _oracle1, address _oracle2) {
 
26:        priceFeed1 = AggregatorV3Interface(_oracle1);
27:        priceFeed2 = AggregatorV3Interface(_oracle2);
28:        require(
29:            (priceFeed1.decimals() == priceFeed2.decimals()), //@audit: priceFeed1.decimals() Initial call
30:            "Decimals must be the same"
31:        );

36:        decimals = priceFeed1.decimals(); //@audit: priceFeed1.decimals() Second call
37:    }
```

```diff

diff --git a/src/oracles/PegOracle.sol b/src/oracles/PegOracle.sol
index 1c65268..71af57e 100644
--- a/src/oracles/PegOracle.sol
+++ b/src/oracles/PegOracle.sol
@@ -25,15 +25,16 @@ contract PegOracle {
         require(_oracle1 != _oracle2, "Cannot be same Oracle");
         priceFeed1 = AggregatorV3Interface(_oracle1);
         priceFeed2 = AggregatorV3Interface(_oracle2);
+        uint8 priceFeed1Decimal = priceFeed1.decimals();
         require(
-            (priceFeed1.decimals() == priceFeed2.decimals()),
+            (priceFeed1Decimal == priceFeed2.decimals()),
             "Decimals must be the same"
         );

         oracle1 = _oracle1;
         oracle2 = _oracle2;

-        decimals = priceFeed1.decimals();
+        decimals = priceFeed1Decimal;
     }

     /** @notice Returns oracle-fed data from the latest round
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L198-L207
### Controller.sol.triggerEndEpoch(): The result of vaultFactory.getVaults(marketIndex)  should be cached
```solidity
File: /src/Controller.sol
198:    function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
199:        if(
200:            vaultFactory.getVaults(marketIndex).length != VAULTS_LENGTH) //@audit: Called here
201:                revert MarketDoesNotExist(marketIndex);
202:        if(
203:            block.timestamp < epochEnd)
204:            revert EpochNotExpired();

206:        address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex); //@audit: Again called here

```


### Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
### Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L132-L139
```solidity
File: /src/rewards/StakingRewards.sol
132:    function getReward() public nonReentrant updateReward(msg.sender) {
133:        uint256 reward = rewards[msg.sender]; //@audit: Cache rewards[msg.sender] in local storage
134:        if (reward > 0) {
135:            rewards[msg.sender] = 0; //@audit: rewards[msg.sender] use cached value
136:            rewardsToken.safeTransfer(msg.sender, reward);
137:            emit RewardPaid(msg.sender, reward);
138:        }
139:    }
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L307-L325
### Vault.sol.createAssets(): idExists[epochEnd] should be cached
```solidity
File: /src/Vault.sol
    function createAssets(uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee)
        public
        onlyFactory
    {
  
        if(idExists[epochEnd] == true) //@audit: 1st access(SLOAD)
            revert MarketEpochExists();
        
        if(epochBegin >= epochEnd)
            revert EpochEndMustBeAfterBegin();


        idExists[epochEnd] = true; //@audit:  2nd access/SSTORE
        idEpochBegin[epochEnd] = epochBegin;
        epochs.push(epochEnd);


        epochFee[epochEnd] = _withdrawalFee;
    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L221-L223
### VaultFactory.sol.createNewMarket(): tokenToOracle[\_token] should be cached
```solidity
File: /src/VaultFactory.sol
221:        if (tokenToOracle[_token] == address(0)) {
222:            tokenToOracle[_token] = _oracle;
223:        }
```

### Use calldata instead of memory for function parameters
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having memory arguments, it's still valid for implementation contracs to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is public but can be marked as external since it's not called by the contract, and cases where a constructor is involved

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L65-L73
```solidity
File: /src/SemiFungibleVault.sol

//@audit: _name and _symbol should use calldata
65:    constructor(
66:        ERC20 _asset,
67:        string memory _name,
68:        string memory _symbol
69:    ) ERC1155("") {
70:        asset = _asset;
71:        name = _name;
72:        symbol = _symbol;
73:    }
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L114-L122
```solidity
File: /src/Vault.sol

//@audit: _name and _symbol should use calldata
114:   constructor(
115:        address _assetAddress,
116:        string memory _name,
117:        string memory _symbol,
118:        address _treasury,
119:        address _token,
120:        int256 _strikePrice,
121:        address _controller
122:    ) SemiFungibleVault(ERC20(_assetAddress), _name, _symbol) {
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L178-L186
```solidity
File: /src/VaultFactory.sol

//@audit: use calldata for _name
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


### Emitting storage values instead of the memory one.
Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L225-L232
```solidity
File: /src/rewards/StakingRewards.sol
225:    function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner {
226:        require(
227:            block.timestamp > periodFinish,
228:            "Previous rewards period must be complete before changing the duration for the new period"
229:        );
230:        rewardsDuration = _rewardsDuration;
231:        emit RewardsDurationUpdated(rewardsDuration);
232:    }
```

We should emit `_rewardsDuration` instead of `rewardsDuration`

### Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L43-L44
```solidity
File: /src/rewards/StakingRewards.sol
43:    mapping(address => uint256) public userRewardPerTokenPaid;
44:    mapping(address => uint256) public rewards;
```

### Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L313
```solidity
File: /src/VaultFactory.sol
313:        address[] memory vaults = indexVaults[_marketIndex];

331:        address[] memory vaults = indexVaults[_marketIndex];

352:        address[] memory vaults = indexVaults[_marketIndex];
```


### x += y costs more gas than x = x + y for state variables

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L195
```solidity
File: /src/VaultFactory.sol
195:        marketIndex += 1;
```

### No need to initialize state  variables with their default values
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). If you explicitly initialize it with its default value, you are just wasting gas.
It costs more gas to initialize variables to zero than to let the default of zero be applied
Declaring uint256 i = 0; means doing an MSTORE of the value 0 Instead you could just declare uint256 i to declare the variable without assigning it’s default value, saving 3 gas per declaration

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L36
```solidity
File: /src/rewards/StakingRewards.sol
36:    uint256 public periodFinish = 0;
```

### Get rid of safemath as the over/underflow checks are in built in solidity 0.8+
We can save on some gas by getting rid of safemath library and using unchecked blocks for arithmetic operations that can never overflow/underflow
SafeMath  is no longer needed starting with Solidity 0.8. The compiler now has built in overflow checking.

If we get rid of safemath we can optimize some functions to save on gas. Since the compiler will check for under/overflows by default, we can disable this checks for arithmetic operations that are guaranteed to never over/underflow. This would include the following

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L4
```solidity
File: /src/rewards/StakingRewards.sol
4:     import {SafeMath} from "@openzeppelin/contracts/utils/math/SafeMath.sol";

29:    using SafeMath for uint256;

```

### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L192
```solidity
File: /src/rewards/StakingRewards.sol
192:            uint256 remaining = periodFinish.sub(block.timestamp);
```

The use of safeMath **sub** function is just an overkill in the above  due to the following:
1. The compiler has in builtin checks for overflows/underflows which the safeMath library is also trying to check for.
2. The operation in itself cannot underflow as we have a check on [Line 189](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L189) ensuring that `periodFinish` is greater or equal to `block.timestamp` before perfoming the operation.

### Recommendation
1. Get rid of safeMath library 
2.  Wrap the operation `periodFinish - block.timestamp` with unchecked blocks

### Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . 

**Affected code**
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L443-L450
```solidity
File: /src/Vault.sol
443:        for (uint256 i = 0; i < epochsLength(); i++) {
444:            if (epochs[i] == _epoch) {
445:                if (i == epochsLength() - 1) {
446:                    return 0;
447:                }
448:                return epochs[i + 1];
449:            }
450:        }
```

[see resource](https://github.com/ethereum/solidity/issues/10695)
### ++i costs less gas compared to i++ or i += 1  (~5 gas per iteration)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i. Which means:

```solidity
uint i = 1;  
i++; // == 1 but i == 2  
```

But ++i returns the actual incremented value:

```solidity
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

Instances include:

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L443
```solidity
File: /src/Vault.sol
443:        for (uint256 i = 0; i < epochsLength(); i++) {
```

### Boolean comparisons

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value.
I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here:

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L93
```solidity
File: /src/Controller.sol
93:        if(vault.idExists(epochEnd) == false)

211:        if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L80
```solidity
File: /src/Vault.sol
80:        if(idExists[id] != true)

96:        if((block.timestamp < id) && idDepegged[id] == false)

314:        if(idExists[epochEnd] == true)
```

### Using bools for storage incurs overhead
 Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

**Instances affected include**
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L52-L54
```solidity
File: /src/Vault.sol
52:    mapping(uint256 => bool) public idDepegged;

54:    mapping(uint256 => bool) public idExists;
```

### A modifier used only once and not being inherited should be inlined to save gas

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L52-L56
```solidity
File: /src/rewards/RewardsFactory.sol
52:    modifier onlyAdmin() {
53:        if(msg.sender != admin)
54:            revert AddressNotAdmin();
55:        _;
56:    }
```

The above modifer is only used on [Line 85](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L85)

### Unused Modifier
The following modifier wastes deployement gas as it is not being invoked on the entire codebase

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L73-L77
```solidity
File: /src/Controller.sol
73:    modifier onlyAdmin() {
74:        if(msg.sender != admin)
75:            revert AddressNotAdmin();
76:        _;
77:    }
```
### Using private rather than public for constants, saves gas
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L16
```solidity
File: /src/Controller.sol
16:    uint256 public constant VAULTS_LENGTH = 2;
```


### Use shorter revert strings(less than 32 bytes) 
Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.Each extra chunk of byetes past the original 32 incurs an MSTORE which costs 3 gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L226-L229
```solidity
File: /src/rewards/StakingRewards.sol


217:        require(
218:            tokenAddress != address(stakingToken),
219:            "Cannot withdraw the staking token"
220:        );


226:        require(
227:            block.timestamp > periodFinish,
228:            "Previous rewards period must be complete before changing the duration for the new period"
229:        );
```
**Other instances to modify**
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L23-L24
```solidity
File: /src/oracles/PegOracle.sol
23:        require(_oracle1 != address(0), "oracle1 cannot be the zero address");
24:        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```

I suggest shortening the revert strings to fit in 32 bytes, or using custom errors.

## Use Custom Errors instead of Revert Strings to save Gas(~50 gas)
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L96
```solidity
File: /src/rewards/StakingRewards.sol
96:        require(amount != 0, "Cannot stake 0");

119:        require(amount > 0, "Cannot withdraw 0");

202:        require(
203:            rewardRate <= balance.div(rewardsDuration),
204:            "Provided reward too high"
205:        );

217:        require(
218:            tokenAddress != address(stakingToken),
219:            "Cannot withdraw the staking token"
220:        );


226:        require(
227:            block.timestamp > periodFinish,
228:            "Previous rewards period must be complete before changing the duration for the new period"
229:        );
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L91
```solidity
File: /src/SemiFungibleVault.sol
91:        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");


116:        require(
117:            msg.sender == owner || isApprovedForAll(owner, receiver),
118:            "Only owner can withdraw, or owner has approved receiver for all"
119:        );
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L165
```solidity
File: /src/Vault.sol
165:        require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");

187:         require(msg.value > 0, "ZeroValue");
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L23-L25
```solidity
File: /src/oracles/PegOracle.sol
23:        require(_oracle1 != address(0), "oracle1 cannot be the zero address");
24:        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
25:        require(_oracle1 != _oracle2, "Cannot be same Oracle");

28:        require(
29:            (priceFeed1.decimals() == priceFeed2.decimals()),
30:            "Decimals must be the same"
31:        );


98:        require(price1 > 0, "Chainlink price <= 0");
99:        require(
100:            answeredInRound1 >= roundID1,
101:            "RoundID from Oracle is outdated!"
102:        );
103:        require(timeStamp1 != 0, "Timestamp == 0 !");


121:        require(price2 > 0, "Chainlink price <= 0");
122:        require(
123:            answeredInRound2 >= roundID2,
124:            "RoundID from Oracle is outdated!"
125:        );
126:        require(timeStamp2 != 0, "Timestamp == 0 !");
```