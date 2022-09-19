##### Gas Optimizations

Gas savings are estimated using the gas report of existing `forge test --gas-report --fork-url https://arb1.arbitrum.io/rpc` tests (the sum of all deployment costs and the sum of the costs of calling methods) and may vary depending on the implementation of the fix.

|       | Issue                                                                                        | Instances | Estimated gas(deployments) | Estimated gas(avg method call) |
| :---: | :------------------------------------------------------------------------------------------- | :-------: | :------------------------: | :----------------------------: |
| **1** | Use custom errors rather than revert()/require() strings to save gas                         |    13     |          105 127           |            134 279             |
| **2** | Cache the results of an external function instead of calling it again                        |     5     |          102 794           |             7 512              |
| **3** | Modifiers are redundant if used only once or not used at all.                                |     5     |           33 649           |             2 700              |
| **4** | Using bools for storage incurs overhead                                                      |     1     |           31 843           |             11 635             |
| **5** | State variables should be cached in stack variables rather than re-reading them from storage |     9     |           28 932           |             27 525             |

**Total: 33 instances over 5 issues**

---

#### 1. **Use custom errors rather than revert()/require() strings to save gas (13 instances)**

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

- Deployment. Gas Saved: **105 127**

- Minumal Method Call. Gas Saved: **72**

- Average Method Call. Gas Saved: **134 279**

- Maximum Method Call. Gas Saved: **139 061**

##### - src/SemiFungibleVault.sol:[91](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L91), [116-119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L116-L119)

```diff
diff --git a/src/SemiFungibleVault.sol b/src/SemiFungibleVault.sol
index caf8eb7..da2bc98 100644
--- a/src/SemiFungibleVault.sol
+++ b/src/SemiFungibleVault.sol
@@ -9,6 +9,9 @@ import {
    9,   9: } from "@openzeppelin/contracts/token/ERC1155/extensions/ERC1155Supply.sol";
   10,  10: import {ERC1155} from "@openzeppelin/contracts/token/ERC1155/ERC1155.sol";
   11,  11:
+       12:+error OnlyOwnerOrApproved();
+       13:+error ZeroShares();
+       14:+
   12,  15: abstract contract SemiFungibleVault is ERC1155Supply {
   13,  16:     using SafeTransferLib for ERC20;
   14,  17:     using FixedPointMathLib for uint256;
@@ -88,7 +91,7 @@ abstract contract SemiFungibleVault is ERC1155Supply {
   88,  91:         address receiver
   89,  92:     ) public virtual returns (uint256 shares) {
   90,  93:         // Check for rounding error since we round down in previewDeposit.
-  91     :-        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
+       94:+        if((shares = previewDeposit(id, assets)) == 0) revert ZeroShares();
   92,  95:
   93,  96:         // Need to transfer before minting or ERC777s could reenter.
   94,  97:         asset.safeTransferFrom(msg.sender, address(this), assets);
@@ -113,10 +116,8 @@ abstract contract SemiFungibleVault is ERC1155Supply {
  113, 116:         address receiver,
  114, 117:         address owner
  115, 118:     ) external virtual returns (uint256 shares) {
- 116     :-        require(
- 117     :-            msg.sender == owner || isApprovedForAll(owner, receiver),
- 118     :-            "Only owner can withdraw, or owner has approved receiver for all"
- 119     :-        );
+      119:+        if(msg.sender != owner && !isApprovedForAll(owner, receiver)) revert OnlyOwnerOrApproved();
+      120:+
  120, 121:
  121, 122:         shares = previewWithdraw(id, assets); // No need to check for rounding error, previewWithdraw rounds up.
  122, 123:
```

##### - src/Vault.sol:[165](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L165), [187](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L187)

```diff
diff --git a/src/Vault.sol b/src/Vault.sol
index 1d2e6df..a7e58b9 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -162,7 +162,7 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
  162, 162:         returns (uint256 shares)
  163, 163:     {
  164, 164:         // Check for rounding error since we round down in previewDeposit.
- 165     :-        require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
+      165:+        if((shares = previewDeposit(id, assets)) == 0) revert ZeroValue();
  166, 166:
  167, 167:         asset.transferFrom(msg.sender, address(this), shares);
  168, 168:
@@ -184,7 +184,7 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
  184, 184:         payable
  185, 185:         returns (uint256 shares)
  186, 186:     {
- 187     :-        require(msg.value > 0, "ZeroValue");
+      187:+        if(msg.value == 0) revert ZeroValue();
  188, 188:
  189, 189:         IWETH(address(asset)).deposit{value: msg.value}();
  190, 190:         assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
```

##### - src/oracles/PegOracle.sol:[23-25](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L23-L25), [28-31](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L28-L31), [98-103](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L98-L103), [121-126](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L121-L126)

```diff
diff --git a/src/oracles/PegOracle.sol b/src/oracles/PegOracle.sol
index 1c65268..31f1362 100644
--- a/src/oracles/PegOracle.sol
+++ b/src/oracles/PegOracle.sol
@@ -3,6 +3,13 @@ pragma solidity 0.8.15;
    3,   3:
    4,   4: import "@chainlink/interfaces/AggregatorV3Interface.sol";
    5,   5:
+        6:+error ZeroTimestamp();
+        7:+error OutdatedOracle();
+        8:+error InvalidPrice();
+        9:+error InvalidDecimals();
+       10:+error SameOracle();
+       11:+error ZeroAddress();
+       12:+
    6,  13: contract PegOracle {
    7,  14:     /***
    8,  15:     @dev  for example: oracle1 would be stETH / USD, while oracle2 would be ETH / USD oracle
@@ -20,15 +27,13 @@ contract PegOracle {
   20,  27:       * @param _oracle2 Second oracle address
   21,  28:       */
   22,  29:     constructor(address _oracle1, address _oracle2) {
-  23     :-        require(_oracle1 != address(0), "oracle1 cannot be the zero address");
-  24     :-        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
-  25     :-        require(_oracle1 != _oracle2, "Cannot be same Oracle");
+       30:+        if(_oracle1 == address(0)) revert ZeroAddress();
+       31:+        if(_oracle2 == address(0)) revert ZeroAddress();
+       32:+        if(_oracle1 == _oracle2) revert SameOracle();
   26,  33:         priceFeed1 = AggregatorV3Interface(_oracle1);
   27,  34:         priceFeed2 = AggregatorV3Interface(_oracle2);
-  28     :-        require(
-  29     :-            (priceFeed1.decimals() == priceFeed2.decimals()),
-  30     :-            "Decimals must be the same"
-  31     :-        );
+       35:+        if((priceFeed1.decimals() != priceFeed2.decimals())) revert InvalidDecimals();
+       36:+
   32,  37:
   33,  38:         oracle1 = _oracle1;
   34,  39:         oracle2 = _oracle2;
@@ -95,12 +100,9 @@ contract PegOracle {
   95, 100:             uint80 answeredInRound1
   96, 101:         ) = priceFeed1.latestRoundData();
   97, 102:
-  98     :-        require(price1 > 0, "Chainlink price <= 0");
-  99     :-        require(
- 100     :-            answeredInRound1 >= roundID1,
- 101     :-            "RoundID from Oracle is outdated!"
- 102     :-        );
- 103     :-        require(timeStamp1 != 0, "Timestamp == 0 !");
+      103:+        if(price1 <= 0) revert InvalidPrice();
+      104:+        if(answeredInRound1 < roundID1) revert OutdatedOracle();
+      105:+        if(timeStamp1 == 0) revert ZeroTimestamp();
  104, 106:
  105, 107:         return price1;
  106, 108:     }
@@ -118,12 +120,9 @@ contract PegOracle {
  118, 120:             uint80 answeredInRound2
  119, 121:         ) = priceFeed2.latestRoundData();
  120, 122:
- 121     :-        require(price2 > 0, "Chainlink price <= 0");
- 122     :-        require(
- 123     :-            answeredInRound2 >= roundID2,
- 124     :-            "RoundID from Oracle is outdated!"
- 125     :-        );
- 126     :-        require(timeStamp2 != 0, "Timestamp == 0 !");
+      123:+        if(price2 <= 0) revert InvalidPrice();
+      124:+        if(answeredInRound2 < roundID2) revert OutdatedOracle();
+      125:+        if(timeStamp2 == 0) revert ZeroTimestamp();
  127, 126:
  128, 127:         return price2;
  129, 128:     }
```

##### - src/rewards/StakingRewards.sol:[96](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L96), [119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L119), [202-205](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L202-L205), [217-220](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L217-L220), [226-229](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L226-L229)

```diff
diff --git a/src/rewards/StakingRewards.sol b/src/rewards/StakingRewards.sol
index 5edb4e8..3d59541 100644
--- a/src/rewards/StakingRewards.sol
+++ b/src/rewards/StakingRewards.sol
@@ -18,6 +18,11 @@ import {IERC1155} from "@openzeppelin/contracts/token/ERC1155/IERC1155.sol";
   18,  18: import {ERC20} from "@solmate/tokens/ERC20.sol";
   19,  19: import "./Owned.sol";
   20,  20:
+       21:+error RewardPeriodMustComplite();
+       22:+error InvalidToken();
+       23:+error RewardTooHigh();
+       24:+error ZeroAmount();
+       25:+
   21,  26: // https://docs.synthetix.io/contracts/source/contracts/stakingrewards
   22,  27: contract StakingRewards is
   23,  28:     IStakingRewards,
@@ -93,7 +98,7 @@ contract StakingRewards is
   93,  98:         whenNotPaused
   94,  99:         updateReward(msg.sender)
   95, 100:     {
-  96     :-        require(amount != 0, "Cannot stake 0");
+      101:+        if(amount == 0) revert ZeroAmount();
   97, 102:         _totalSupply = _totalSupply.add(amount);
   98, 103:         _balances[msg.sender] = _balances[msg.sender].add(amount);
   99, 104:         stakingToken.safeTransferFrom(
@@ -116,7 +121,7 @@ contract StakingRewards is
  116, 121:         nonReentrant
  117, 122:         updateReward(msg.sender)
  118, 123:     {
- 119     :-        require(amount > 0, "Cannot withdraw 0");
+      124:+        if(amount == 0) revert ZeroAmount();
  120, 125:         _totalSupply = _totalSupply.sub(amount);
  121, 126:         _balances[msg.sender] = _balances[msg.sender].sub(amount);
  122, 127:         stakingToken.safeTransferFrom(
@@ -199,10 +204,7 @@ contract StakingRewards is
  199, 204:         // very high values of rewardRate in the earned and rewardsPerToken functions;
  200, 205:         // Reward + leftover must be less than 2^256 / 10^18 to avoid overflow.
  201, 206:         uint256 balance = rewardsToken.balanceOf(address(this));
- 202     :-        require(
- 203     :-            rewardRate <= balance.div(rewardsDuration),
- 204     :-            "Provided reward too high"
- 205     :-        );
+      207:+        if(rewardRate > balance.div(rewardsDuration)) revert RewardTooHigh();
  206, 208:
  207, 209:         lastUpdateTime = block.timestamp;
  208, 210:         periodFinish = block.timestamp.add(rewardsDuration);
@@ -214,19 +216,13 @@ contract StakingRewards is
  214, 216:         external
  215, 217:         onlyOwner
  216, 218:     {
- 217     :-        require(
- 218     :-            tokenAddress != address(stakingToken),
- 219     :-            "Cannot withdraw the staking token"
- 220     :-        );
+      219:+        if(tokenAddress == address(stakingToken)) revert InvalidToken();
  221, 220:         ERC20(tokenAddress).safeTransfer(owner, tokenAmount);
  222, 221:         emit Recovered(tokenAddress, tokenAmount);
  223, 222:     }
  224, 223:
  225, 224:     function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner {
- 226     :-        require(
- 227     :-            block.timestamp > periodFinish,
- 228     :-            "Previous rewards period must be complete before changing the duration for the new period"
- 229     :-        );
+      225:+        if(block.timestamp <= periodFinish) revert RewardPeriodMustComplite();
  230, 226:         rewardsDuration = _rewardsDuration;
  231, 227:         emit RewardsDurationUpdated(rewardsDuration);
  232, 228:     }
```

#### 2. **Cache the results of an external function instead of calling it again (5 instances)**

- Deployment. Gas Saved: **102 794**

- Minumal Method Call. Gas Saved: **-53**

- Average Method Call. Gas Saved: **7 512**

- Maximum Method Call. Gas Saved: **7 514**

##### - src/Controller.sol:[199-200](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L199-L200), [202-203](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L202-L203), [206-209](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L206-L209)

```diff
diff --git a/src/Controller.sol b/src/Controller.sol
index e15b0fa..54503d7 100644
--- a/src/Controller.sol
+++ b/src/Controller.sol
@@ -76,39 +76,6 @@ contract Controller {
   76,  76:         _;
   77,  77:     }
   78,  78:
-  79     :-    /** @notice Modifier to ensure market exists, current market epoch time and price are valid
-  80     :-      * @param marketIndex Target market index
-  81     :-      * @param epochEnd End of epoch set for market
-  82     :-      */
-  83     :-    modifier isDisaster(uint256 marketIndex, uint256 epochEnd) {
-  84     :-        address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
-  85     :-        if(
-  86     :-            vaultsAddress.length != VAULTS_LENGTH
-  87     :-            )
-  88     :-            revert MarketDoesNotExist(marketIndex);
-  89     :-
-  90     :-        address vaultAddress = vaultsAddress[0];
-  91     :-        Vault vault = Vault(vaultAddress);
-  92     :-
-  93     :-        if(vault.idExists(epochEnd) == false)
-  94     :-            revert EpochNotExist();
-  95     :-
-  96     :-        if(
-  97     :-            vault.strikePrice() < getLatestPrice(vault.tokenInsured())
-  98     :-            )
-  99     :-            revert PriceNotAtStrikePrice(getLatestPrice(vault.tokenInsured()));
- 100     :-
- 101     :-        if(
- 102     :-            vault.idEpochBegin(epochEnd) > block.timestamp)
- 103     :-            revert EpochNotStarted();
- 104     :-
- 105     :-        if(
- 106     :-            block.timestamp > epochEnd
- 107     :-            )
- 108     :-            revert EpochExpired();
- 109     :-        _;
- 110     :-    }
- 111     :-
  112,  79:     /*//////////////////////////////////////////////////////////////
  113,  80:                                 CONSTRUCTOR
  114,  81:     //////////////////////////////////////////////////////////////*/
@@ -147,12 +114,27 @@ contract Controller {
  147, 114:       */
  148, 115:     function triggerDepeg(uint256 marketIndex, uint256 epochEnd)
  149, 116:         public
- 150     :-        isDisaster(marketIndex, epochEnd)
  151, 117:     {
  152, 118:         address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
+      119:+        if(vaultsAddress.length != VAULTS_LENGTH)
+      120:+            revert MarketDoesNotExist(marketIndex);
+      121:+
  153, 122:         Vault insrVault = Vault(vaultsAddress[0]);
  154, 123:         Vault riskVault = Vault(vaultsAddress[1]);
  155, 124:
+      125:+        if(insrVault.idExists(epochEnd) == false)
+      126:+            revert EpochNotExist();
+      127:+
+      128:+        int256 nowPrice;
+      129:+        if(insrVault.strikePrice() < (nowPrice = getLatestPrice(insrVault.tokenInsured())))
+      130:+            revert PriceNotAtStrikePrice(nowPrice);
+      131:+
+      132:+        if(insrVault.idEpochBegin(epochEnd) > block.timestamp)
+      133:+            revert EpochNotStarted();
+      134:+
+      135:+        if(block.timestamp > epochEnd)
+      136:+            revert EpochExpired();
+      137:+
  156, 138:         //require this function cannot be called twice in the same epoch for the same vault
  157, 139:         if(insrVault.idFinalTVL(epochEnd) != 0)
  158, 140:             revert NotZeroTVL();
@@ -196,17 +178,14 @@ contract Controller {
  196, 178:       * @param epochEnd End of epoch set for market
  197, 179:       */
  198, 180:     function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
- 199     :-        if(
- 200     :-            vaultFactory.getVaults(marketIndex).length != VAULTS_LENGTH)
+      181:+        address[] memory vaults = vaultFactory.getVaults(marketIndex);
+      182:+        if(vaults.length != VAULTS_LENGTH)
  201, 183:                 revert MarketDoesNotExist(marketIndex);
- 202     :-        if(
- 203     :-            block.timestamp < epochEnd)
+      184:+        if(block.timestamp < epochEnd)
  204, 185:             revert EpochNotExpired();
  205, 186:
- 206     :-        address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
- 207     :-
- 208     :-        Vault insrVault = Vault(vaultsAddress[0]);
- 209     :-        Vault riskVault = Vault(vaultsAddress[1]);
+      187:+        Vault insrVault = Vault(vaults[0]);
+      188:+        Vault riskVault = Vault(vaults[1]);
  210, 189:
  211, 190:         if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
  212, 191:             revert EpochNotExist();
```

##### - src/oracles/PegOracle.sol:[29](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L29)

```diff
diff --git a/src/oracles/PegOracle.sol b/src/oracles/PegOracle.sol
index 1c65268..8387ed9 100644
--- a/src/oracles/PegOracle.sol
+++ b/src/oracles/PegOracle.sol
@@ -25,15 +25,16 @@ contract PegOracle {
   25,  25:         require(_oracle1 != _oracle2, "Cannot be same Oracle");
   26,  26:         priceFeed1 = AggregatorV3Interface(_oracle1);
   27,  27:         priceFeed2 = AggregatorV3Interface(_oracle2);
+       28:+        uint8 _decimals;
   28,  29:         require(
-  29     :-            (priceFeed1.decimals() == priceFeed2.decimals()),
+       30:+            ((_decimals = priceFeed1.decimals()) == priceFeed2.decimals()),
   30,  31:             "Decimals must be the same"
   31,  32:         );
   32,  33:
   33,  34:         oracle1 = _oracle1;
   34,  35:         oracle2 = _oracle2;
   35,  36:
-  36     :-        decimals = priceFeed1.decimals();
+       37:+        decimals = _decimals;
   37,  38:     }
   38,  39:
   39,  40:     /** @notice Returns oracle-fed data from the latest round
```

##### - src/rewards/RewardsFactory.sol:[90-91](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L90-L91)

```diff
diff --git a/src/rewards/RewardsFactory.sol b/src/rewards/RewardsFactory.sol
index 8bee8bd..a679348 100644
--- a/src/rewards/RewardsFactory.sol
+++ b/src/rewards/RewardsFactory.sol
@@ -87,8 +87,9 @@ contract RewardsFactory {
   87,  87:     {
   88,  88:         VaultFactory vaultFactory = VaultFactory(factory);
   89,  89:
-  90     :-        address _insrToken = vaultFactory.getVaults(_marketIndex)[0];
-  91     :-        address _riskToken = vaultFactory.getVaults(_marketIndex)[1];
+       90:+        address[] memory vaults = vaultFactory.getVaults(_marketIndex);
+       91:+        address _insrToken = vaults[0];
+       92:+        address _riskToken = vaults[1];
   92,  93:
   93,  94:         if(_insrToken == address(0) || _riskToken == address(0))
   94,  95:             revert MarketDoesNotExist(_marketIndex);
```

#### 3. **Modifiers are redundant if used only once or not used at all. (5 instances)**

- Deployment. Gas Saved: **33 649**

- Minumal Method Call. Gas Saved: **-202**

- Average Method Call. Gas Saved: **2 700**

- Maximum Method Call. Gas Saved: **4 931**

##### - src/Controller.sol:[67-111](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L67-L111)

```diff
diff --git a/src/Controller.sol b/src/Controller.sol
index e15b0fa..b8cafb5 100644
--- a/src/Controller.sol
+++ b/src/Controller.sol
@@ -64,51 +64,6 @@ contract Controller {
   64,  64:     }
   65,  65:     /* solhint-enable  var-name-mixedcase */
   66,  66:
-  67     :-    /*//////////////////////////////////////////////////////////////
-  68     :-                                 MODIFIERS
-  69     :-    //////////////////////////////////////////////////////////////*/
-  70     :-
-  71     :-    /** @notice Only admin addresses can call functions that use this modifier
-  72     :-      */
-  73     :-    modifier onlyAdmin() {
-  74     :-        if(msg.sender != admin)
-  75     :-            revert AddressNotAdmin();
-  76     :-        _;
-  77     :-    }
-  78     :-
-  79     :-    /** @notice Modifier to ensure market exists, current market epoch time and price are valid
-  80     :-      * @param marketIndex Target market index
-  81     :-      * @param epochEnd End of epoch set for market
-  82     :-      */
-  83     :-    modifier isDisaster(uint256 marketIndex, uint256 epochEnd) {
-  84     :-        address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
-  85     :-        if(
-  86     :-            vaultsAddress.length != VAULTS_LENGTH
-  87     :-            )
-  88     :-            revert MarketDoesNotExist(marketIndex);
-  89     :-
-  90     :-        address vaultAddress = vaultsAddress[0];
-  91     :-        Vault vault = Vault(vaultAddress);
-  92     :-
-  93     :-        if(vault.idExists(epochEnd) == false)
-  94     :-            revert EpochNotExist();
-  95     :-
-  96     :-        if(
-  97     :-            vault.strikePrice() < getLatestPrice(vault.tokenInsured())
-  98     :-            )
-  99     :-            revert PriceNotAtStrikePrice(getLatestPrice(vault.tokenInsured()));
- 100     :-
- 101     :-        if(
- 102     :-            vault.idEpochBegin(epochEnd) > block.timestamp)
- 103     :-            revert EpochNotStarted();
- 104     :-
- 105     :-        if(
- 106     :-            block.timestamp > epochEnd
- 107     :-            )
- 108     :-            revert EpochExpired();
- 109     :-        _;
- 110     :-    }
- 111     :-
  112,  67:     /*//////////////////////////////////////////////////////////////
  113,  68:                                 CONSTRUCTOR
  114,  69:     //////////////////////////////////////////////////////////////*/
@@ -147,9 +102,32 @@ contract Controller {
  147, 102:       */
  148, 103:     function triggerDepeg(uint256 marketIndex, uint256 epochEnd)
  149, 104:         public
- 150     :-        isDisaster(marketIndex, epochEnd)
  151, 105:     {
  152, 106:         address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
+      107:+        if(
+      108:+            vaultsAddress.length != VAULTS_LENGTH
+      109:+            )
+      110:+            revert MarketDoesNotExist(marketIndex);
+      111:+
+      112:+        address vaultAddress = vaultsAddress[0];
+      113:+        Vault vault = Vault(vaultAddress);
+      114:+
+      115:+        if(vault.idExists(epochEnd) == false)
+      116:+            revert EpochNotExist();
+      117:+
+      118:+        if(
+      119:+            vault.strikePrice() < getLatestPrice(vault.tokenInsured())
+      120:+            )
+      121:+            revert PriceNotAtStrikePrice(getLatestPrice(vault.tokenInsured()));
+      122:+
+      123:+        if(
+      124:+            vault.idEpochBegin(epochEnd) > block.timestamp)
+      125:+            revert EpochNotStarted();
+      126:+
+      127:+        if(
+      128:+            block.timestamp > epochEnd
+      129:+            )
+      130:+            revert EpochExpired();
  153, 131:         Vault insrVault = Vault(vaultsAddress[0]);
  154, 132:         Vault riskVault = Vault(vaultsAddress[1]);
  155, 133:
```

##### - src/Vault.sol:[85-100](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L85-L100)

```diff
diff --git a/src/Vault.sol b/src/Vault.sol
index 1d2e6df..9e480ed 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -82,22 +82,6 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
   82,  82:         _;
   83,  83:     }
   84,  84:
-  85     :-    /** @notice You can only call functions that use this modifier before the current epoch has started
-  86     :-      */
-  87     :-    modifier epochHasNotStarted(uint256 id) {
-  88     :-        if(block.timestamp > idEpochBegin[id] - timewindow)
-  89     :-            revert EpochAlreadyStarted();
-  90     :-        _;
-  91     :-    }
-  92     :-
-  93     :-    /** @notice You can only call functions that use this modifier after the current epoch has started
-  94     :-      */
-  95     :-    modifier epochHasEnded(uint256 id) {
-  96     :-        if((block.timestamp < id) && idDepegged[id] == false)
-  97     :-            revert EpochNotFinished();
-  98     :-        _;
-  99     :-    }
- 100     :-
  101,  85:     /*//////////////////////////////////////////////////////////////
  102,  86:                                  CONSTRUCTOR
  103,  87:     //////////////////////////////////////////////////////////////*/
@@ -157,10 +141,11 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
  157, 141:         public
  158, 142:         override
  159, 143:         marketExists(id)
- 160     :-        epochHasNotStarted(id)
  161, 144:         nonReentrant
  162, 145:         returns (uint256 shares)
  163, 146:     {
+      147:+        if(block.timestamp > idEpochBegin[id] - timewindow)
+      148:+            revert EpochAlreadyStarted();
  164, 149:         // Check for rounding error since we round down in previewDeposit.
  165, 150:         require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
  166, 151:
@@ -208,10 +193,11 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
  208, 193:     )
  209, 194:         external
  210, 195:         override
- 211     :-        epochHasEnded(id)
  212, 196:         marketExists(id)
  213, 197:         returns (uint256 shares)
  214, 198:     {
+      199:+        if((block.timestamp < id) && idDepegged[id] == false)
+      200:+            revert EpochNotFinished();
  215, 201:         if(
  216, 202:             msg.sender != owner &&
  217, 203:             isApprovedForAll(owner, receiver) == false)
```

##### - src/rewards/RewardsFactory.sol:[50-56](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L50-L56)

```diff
diff --git a/src/rewards/RewardsFactory.sol b/src/rewards/RewardsFactory.sol
index 8bee8bd..35c45b4 100644
--- a/src/rewards/RewardsFactory.sol
+++ b/src/rewards/RewardsFactory.sol
@@ -47,13 +47,6 @@ contract RewardsFactory {
   47,  47:                                   MODIFIERS
   48,  48:     //////////////////////////////////////////////////////////////*/
   49,  49:
-  50     :-    /** @notice Only admin addresses can call functions with this modifier
-  51     :-      */
-  52     :-    modifier onlyAdmin() {
-  53     :-        if(msg.sender != admin)
-  54     :-            revert AddressNotAdmin();
-  55     :-        _;
-  56     :-    }
   57,  50:
   58,  51:     /** @notice Contract constructor
   59,  52:       * @param _govToken Governance token address
@@ -82,9 +75,10 @@ contract RewardsFactory {
   82,  75:       */
   83,  76:     function createStakingRewards(uint256 _marketIndex, uint256 _epochEnd, uint256 _rewardDuration, uint256 _rewardRate)
   84,  77:         external
-  85     :-        onlyAdmin
   86,  78:         returns (address insr, address risk)
   87,  79:     {
+       80:+        if(msg.sender != admin)
+       81:+            revert AddressNotAdmin();
   88,  82:         VaultFactory vaultFactory = VaultFactory(factory);
   89,  83:
   90,  84:         address _insrToken = vaultFactory.getVaults(_marketIndex)[0];
```

#### 4. **Using bools for storage incurs overhead (1 instance)**

- Deployment. Gas Saved: **31 843**

- Minumal Method Call. Gas Saved: **144**

- Average Method Call. Gas Saved: **11 635**

- Maximum Method Call. Gas Saved: **17 611**

```
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
```

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

##### - src/Vault.sol:[54](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L54)

```diff
diff --git a/src/Controller.sol b/src/Controller.sol
index e15b0fa..64c68f3 100644
--- a/src/Controller.sol
+++ b/src/Controller.sol
@@ -90,7 +90,7 @@ contract Controller {
   90,  90:         address vaultAddress = vaultsAddress[0];
   91,  91:         Vault vault = Vault(vaultAddress);
   92,  92:
-  93     :-        if(vault.idExists(epochEnd) == false)
+       93:+        if(vault.idExists(epochEnd) == 0)
   94,  94:             revert EpochNotExist();
   95,  95:
   96,  96:         if(
@@ -208,7 +208,7 @@ contract Controller {
  208, 208:         Vault insrVault = Vault(vaultsAddress[0]);
  209, 209:         Vault riskVault = Vault(vaultsAddress[1]);
  210, 210:
- 211     :-        if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
+      211:+        if(insrVault.idExists(epochEnd) == 0 || riskVault.idExists(epochEnd) == 0)
  212, 212:             revert EpochNotExist();
  213, 213:
  214, 214:         //require this function cannot be called twice in the same epoch for the same vault
diff --git a/src/Vault.sol b/src/Vault.sol
index 1d2e6df..12af155 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -51,7 +51,7 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
   51,  51:     // @audit id can be uint32
   52,  52:     mapping(uint256 => bool) public idDepegged;
   53,  53:     // @audit id can be uint32
-  54     :-    mapping(uint256 => bool) public idExists;
+       54:+    mapping(uint256 => uint256) public idExists;
   55,  55:     mapping(uint256 => uint256) public epochFee;
   56,  56:
   57,  57:     /*//////////////////////////////////////////////////////////////
@@ -77,7 +77,7 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
   77,  77:     /** @notice Only market addresses can call functions that use this modifier
   78,  78:       */
   79,  79:     modifier marketExists(uint256 id) {
-  80     :-        if(idExists[id] != true)
+       80:+        if(idExists[id] != 1)
   81,  81:             revert MarketEpochDoesNotExist();
   82,  82:         _;
   83,  83:     }
@@ -311,13 +311,13 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
  311, 311:         if(_withdrawalFee > 150)
  312, 312:             revert FeeMoreThan150(_withdrawalFee);
  313, 313:
- 314     :-        if(idExists[epochEnd] == true)
+      314:+        if(idExists[epochEnd] == 1)
  315, 315:             revert MarketEpochExists();
  316, 316:
  317, 317:         if(epochBegin >= epochEnd)
  318, 318:             revert EpochEndMustBeAfterBegin();
  319, 319:
- 320     :-        idExists[epochEnd] = true;
+      320:+        idExists[epochEnd] = 1;
  321, 321:         idEpochBegin[epochEnd] = epochBegin;
  322, 322:         epochs.push(epochEnd);
  323, 323:
diff --git a/src/rewards/RewardsFactory.sol b/src/rewards/RewardsFactory.sol
index 8bee8bd..d463005 100644
--- a/src/rewards/RewardsFactory.sol
+++ b/src/rewards/RewardsFactory.sol
@@ -93,7 +93,7 @@ contract RewardsFactory {
   93,  93:         if(_insrToken == address(0) || _riskToken == address(0))
   94,  94:             revert MarketDoesNotExist(_marketIndex);
   95,  95:
-  96     :-        if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
+       96:+        if(Vault(_insrToken).idExists(_epochEnd) == 0 || Vault(_riskToken).idExists(_epochEnd) == 0)
   97,  97:             revert EpochDoesNotExist();
   98,  98:
   99,  99:         StakingRewards insrStake = new StakingRewards(
```

#### 5. **State variables should be cached in stack variables rather than re-reading them from storage (9 instances)**

- Deployment. Gas Saved: **28 932**

- Minumal Method Call. Gas Saved: **-59**

- Average Method Call. Gas Saved: **27 525**

- Maximum Method Call. Gas Saved: **27 999**

The code can be optimized by minimising the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

##### - src/Vault.sol:[188-190](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L188-L190), [228](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L228)

```diff
diff --git a/src/Vault.sol b/src/Vault.sol
index 1d2e6df..bb4d1f2 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -185,9 +185,9 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
  185, 185:         returns (uint256 shares)
  186, 186:     {
  187, 187:         require(msg.value > 0, "ZeroValue");
- 188     :-
- 189     :-        IWETH(address(asset)).deposit{value: msg.value}();
- 190     :-        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
+      188:+        IWETH iweth = IWETH(address(asset));
+      189:+        iweth.deposit{value: msg.value}();
+      190:+        assert(iweth.transfer(msg.sender, msg.value));
  191, 191:
  192, 192:         return deposit(id, msg.value, receiver);
  193, 193:     }
@@ -225,10 +225,12 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
  225, 225:         //Taking fee from the amount
  226, 226:         uint256 feeValue = calculateWithdrawalFeeValue(entitledShares, id);
  227, 227:         entitledShares = entitledShares - feeValue;
- 228     :-        asset.transfer(treasury, feeValue);
+      228:+
+      229:+        ERC20 _asset = asset;
+      230:+        _asset.transfer(treasury, feeValue);
  229, 231:
  230, 232:         emit Withdraw(msg.sender, receiver, owner, id, assets, entitledShares);
- 231     :-        asset.transfer(receiver, entitledShares);
+      233:+        _asset.transfer(receiver, entitledShares);
  232, 234:
  233, 235:         return entitledShares;
  234, 236:     }
```

##### - src/VaultFactory.sol:[188](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L188), [195](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L195), [203](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L203)

```diff
diff --git a/src/VaultFactory.sol b/src/VaultFactory.sol
index bfd70f1..f5a7616 100644
--- a/src/VaultFactory.sol
+++ b/src/VaultFactory.sol
@@ -184,46 +184,54 @@ contract VaultFactory {
  184, 184:         address _oracle,
  185, 185:         string memory _name
  186, 186:     ) public onlyAdmin returns (address insr, address rsk) {
+      187:+        Vault hedge;
+      188:+        Vault risk;
+      189:+        uint256 _marketIndex;
+      190:+        {
+      191:+        address _controller = controller;
  187, 192:         if(
- 188     :-            IController(controller).getVaultFactory() != address(this)
+      193:+            IController(_controller).getVaultFactory() != address(this)
  189, 194:             )
  190, 195:             revert AddressFactoryNotInController();
  191, 196:
- 192     :-        if(controller == address(0))
+      197:+        if(_controller == address(0))
  193, 198:             revert ControllerNotSet();
  194, 199:
- 195     :-        marketIndex += 1;
+      200:+        _marketIndex = (marketIndex += 1);
  196, 201:
  197, 202:         //y2kUSDC_99*RISK or y2kUSDC_99*HEDGE
  198, 203:
- 199     :-        Vault hedge = new Vault(
+      204:+        address _treasury = treasury;
+      205:+
+      206:+        hedge = new Vault(
  200, 207:             WETH,
  201, 208:             string(abi.encodePacked(_name,"HEDGE")),
  202, 209:             "hY2K",
- 203     :-            treasury,
+      210:+            _treasury,
  204, 211:             _token,
  205, 212:             _strikePrice,
- 206     :-            controller
+      213:+            _controller
  207, 214:         );
  208, 215:
- 209     :-        Vault risk = new Vault(
+      216:+        risk = new Vault(
  210, 217:             WETH,
  211, 218:             string(abi.encodePacked(_name,"RISK")),
  212, 219:             "rY2K",
- 213     :-            treasury,
+      220:+            _treasury,
  214, 221:             _token,
  215, 222:             _strikePrice,
- 216     :-            controller
+      223:+            _controller
  217, 224:         );
+      225:+    }
  218, 226:
- 219     :-        indexVaults[marketIndex] = [address(hedge), address(risk)];
+      227:+        indexVaults[_marketIndex] = [address(hedge), address(risk)];
  220, 228:
  221, 229:         if (tokenToOracle[_token] == address(0)) {
  222, 230:             tokenToOracle[_token] = _oracle;
  223, 231:         }
  224, 232:
  225, 233:         emit MarketCreated(
- 226     :-            marketIndex,
+      234:+            _marketIndex,
  227, 235:             address(hedge),
  228, 236:             address(risk),
  229, 237:             _token,
@@ -231,7 +239,7 @@ contract VaultFactory {
  231, 239:             _strikePrice
  232, 240:         );
  233, 241:
- 234     :-        MarketVault memory marketVault = MarketVault(marketIndex, epochBegin, epochEnd, hedge, risk, _withdrawalFee);
+      242:+        MarketVault memory marketVault = MarketVault(_marketIndex, epochBegin, epochEnd, hedge, risk, _withdrawalFee);
  235, 243:
  236, 244:         _createEpoch(marketVault);
  237, 245:
```

##### - src/oracles/PegOracle.sol:[29](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L29), [36](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L36)

```diff
diff --git a/src/oracles/PegOracle.sol b/src/oracles/PegOracle.sol
index 1c65268..108b041 100644
--- a/src/oracles/PegOracle.sol
+++ b/src/oracles/PegOracle.sol
@@ -26,14 +26,14 @@ contract PegOracle {
   26,  26:         priceFeed1 = AggregatorV3Interface(_oracle1);
   27,  27:         priceFeed2 = AggregatorV3Interface(_oracle2);
   28,  28:         require(
-  29     :-            (priceFeed1.decimals() == priceFeed2.decimals()),
+       29:+            (AggregatorV3Interface(_oracle1).decimals() == AggregatorV3Interface(_oracle2).decimals()),
   30,  30:             "Decimals must be the same"
   31,  31:         );
   32,  32:
   33,  33:         oracle1 = _oracle1;
   34,  34:         oracle2 = _oracle2;
   35,  35:
-  36     :-        decimals = priceFeed1.decimals();
+       36:+        decimals = AggregatorV3Interface(_oracle1).decimals();
   37,  37:     }
   38,  38:
   39,  39:     /** @notice Returns oracle-fed data from the latest round
```

##### - src/rewards/StakingRewards.sol:[160](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L160), [189-190](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L189-L190)

```diff
diff --git a/src/rewards/StakingRewards.sol b/src/rewards/StakingRewards.sol
index 5edb4e8..345821c 100644
--- a/src/rewards/StakingRewards.sol
+++ b/src/rewards/StakingRewards.sol
@@ -157,7 +157,8 @@ contract StakingRewards is
  157, 157:     }
  158, 158:
  159, 159:     function rewardPerToken() public view returns (uint256) {
- 160     :-        if (_totalSupply == 0) {
+      160:+        uint256 totakSupply_;
+      161:+        if ((totakSupply_ = _totalSupply) == 0) {
  161, 162:             return rewardPerTokenStored;
  162, 163:         }
  163, 164:         return
@@ -166,7 +167,7 @@ contract StakingRewards is
  166, 167:                     .sub(lastUpdateTime)
  167, 168:                     .mul(rewardRate)
  168, 169:                     .mul(1e18)
- 169     :-                    .div(_totalSupply)
+      170:+                    .div(totakSupply_)
  170, 171:             );
  171, 172:     }
  172, 173:
@@ -186,12 +187,14 @@ contract StakingRewards is
  186, 187:         onlyRewardsDistribution
  187, 188:         updateReward(address(0))
  188, 189:     {
- 189     :-        if (block.timestamp >= periodFinish) {
- 190     :-            rewardRate = reward.div(rewardsDuration);
+      190:+        uint256 periodFinish_;
+      191:+        uint256 rewardsDuration_ = rewardsDuration;
+      192:+        if (block.timestamp >= (periodFinish_ = periodFinish)) {
+      193:+            rewardRate = reward.div(rewardsDuration_);
  191, 194:         } else {
- 192     :-            uint256 remaining = periodFinish.sub(block.timestamp);
+      195:+            uint256 remaining = periodFinish_.sub(block.timestamp);
  193, 196:             uint256 leftover = remaining.mul(rewardRate);
- 194     :-            rewardRate = reward.add(leftover).div(rewardsDuration);
+      197:+            rewardRate = reward.add(leftover).div(rewardsDuration_);
  195, 198:         }
  196, 199:
  197, 200:         // Ensure the provided reward amount is not more than the balance in the contract.
@@ -200,12 +203,12 @@ contract StakingRewards is
  200, 203:         // Reward + leftover must be less than 2^256 / 10^18 to avoid overflow.
  201, 204:         uint256 balance = rewardsToken.balanceOf(address(this));
  202, 205:         require(
- 203     :-            rewardRate <= balance.div(rewardsDuration),
+      206:+            rewardRate <= balance.div(rewardsDuration_),
  204, 207:             "Provided reward too high"
  205, 208:         );
  206, 209:
  207, 210:         lastUpdateTime = block.timestamp;
- 208     :-        periodFinish = block.timestamp.add(rewardsDuration);
+      211:+        periodFinish = block.timestamp.add(rewardsDuration_);
  209, 212:         emit RewardAdded(reward);
  210, 213:     }
  211, 214:
```
