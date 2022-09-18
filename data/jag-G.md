src/Controller.sol
1. Use unchecked wherever arithmetic operation does not under/over flow.

```git diff
diff --git a/src/Controller.sol b/src/Controller.sol
index e15b0fa..f415c1a 100644
--- a/src/Controller.sol
+++ b/src/Controller.sol
@@ -279,10 +279,12 @@ contract Controller {
             revert SequencerDown();
         }
 
-        // Make sure the grace period has passed after the sequencer is back up.
-        uint256 timeSinceUp = block.timestamp - startedAt;
-        if (timeSinceUp <= GRACE_PERIOD_TIME) {
-            revert GracePeriodNotOver();
+        unchecked {
+            // Make sure the grace period has passed after the sequencer is back up.
+            uint256 timeSinceUp = block.timestamp - startedAt;
+            if (timeSinceUp <= GRACE_PERIOD_TIME) {
+                revert GracePeriodNotOver();
+            }
         }
 
         AggregatorV3Interface priceFeed = AggregatorV3Interface(
@@ -296,8 +298,10 @@ contract Controller {
             uint80 answeredInRound
         ) = priceFeed.latestRoundData();
 
-        uint256 decimals = 10**(18-(priceFeed.decimals()));
-        price = price * int256(decimals);
+        unchecked {
+            uint256 decimals = 10**(18-(priceFeed.decimals()));
+            price = price * int256(decimals);
+        }
 
         if(price <= 0)
             revert OraclePriceZero();
```

src/Vault.sol
1. Use unchecked wherever arithmetic operation does not under/over flow.
2. Use pre increment
3. No need to initialize variable to 0

```git diff
diff --git a/src/Vault.sol b/src/Vault.sol
index 1d2e6df..f8117ed 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -224,7 +224,9 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
 
         //Taking fee from the amount
         uint256 feeValue = calculateWithdrawalFeeValue(entitledShares, id);
-        entitledShares = entitledShares - feeValue;
+        unchecked {
+            entitledShares = entitledShares - feeValue;
+        }
         asset.transfer(treasury, feeValue);
 
         emit Withdraw(msg.sender, receiver, owner, id, assets, entitledShares);
@@ -440,12 +442,14 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
         view
         returns (uint256 nextEpochEnd)
     {
-        for (uint256 i = 0; i < epochsLength(); i++) {
-            if (epochs[i] == _epoch) {
-                if (i == epochsLength() - 1) {
-                    return 0;
+        unchecked {
+            for (uint256 i; i < epochsLength(); ++i) {
+                if (epochs[i] == _epoch) {
+                    if (i == epochsLength() - 1) {
+                        return 0;
+                    }
+                    return epochs[i + 1];
                 }
-                return epochs[i + 1];
             }
         }
     }
```
src/VaultFactory.sol
1. Use local variable to cache state variable.
Can also cache state variable "treasury". But got "stack too deep" error.
2. Use pre increment.
3. Use unchecked for pre increment.


```git diff
diff --git a/src/VaultFactory.sol b/src/VaultFactory.sol
index bfd70f1..2754ed2 100644
--- a/src/VaultFactory.sol
+++ b/src/VaultFactory.sol
@@ -184,16 +184,18 @@ contract VaultFactory {
         address _oracle,
         string memory _name
     ) public onlyAdmin returns (address insr, address rsk) {
+        address _controller = controller;
         if(
-            IController(controller).getVaultFactory() != address(this)
+            IController(_controller).getVaultFactory() != address(this)
             )
             revert AddressFactoryNotInController();
 
-        if(controller == address(0))
+        if(_controller == address(0))
             revert ControllerNotSet();
 
-        marketIndex += 1;
-
+        unchecked {
+            ++marketIndex;
+        }
         //y2kUSDC_99*RISK or y2kUSDC_99*HEDGE
 
         Vault hedge = new Vault(
@@ -203,7 +205,7 @@ contract VaultFactory {
             treasury,
             _token,
             _strikePrice,
-            controller
+            _controller
         );
 
         Vault risk = new Vault(
@@ -213,7 +215,7 @@ contract VaultFactory {
             treasury,
             _token,
             _strikePrice,
-            controller
+            _controller
         );
 
         indexVaults[marketIndex] = [address(hedge), address(risk)];
```
src/oracles/PegOracle.sol
1. Use unchecked wherever arithmetic operation does not under/over flow.

```git diff
diff --git a/src/oracles/PegOracle.sol b/src/oracles/PegOracle.sol
index 1c65268..f8f936a 100644
--- a/src/oracles/PegOracle.sol
+++ b/src/oracles/PegOracle.sol
@@ -70,8 +70,10 @@ contract PegOracle {
             nowPrice = (price1 * 10000) / price2;
         }
 
-        int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
-        nowPrice = nowPrice * decimals10;
+        unchecked {
+            int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
+            nowPrice = nowPrice * decimals10;
+        }
 
         return (
             roundID1,
```
src/rewards/RewardsFactory.sol
1. Use local variable to cache state variable.
Can also cache state variable "govToken". But got "stack too deep" error.

```git diff
diff --git a/src/rewards/RewardsFactory.sol b/src/rewards/RewardsFactory.sol
index 8bee8bd..42433f7 100644
--- a/src/rewards/RewardsFactory.sol
+++ b/src/rewards/RewardsFactory.sol
@@ -96,9 +96,10 @@ contract RewardsFactory {
         if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
             revert EpochDoesNotExist();
 
+        address _admin = admin;
         StakingRewards insrStake = new StakingRewards(
-            admin,
-            admin,
+            _admin,
+            _admin,
             govToken,
             _insrToken,
             _epochEnd,
@@ -106,8 +107,8 @@ contract RewardsFactory {
             _rewardRate
         );
         StakingRewards riskStake = new StakingRewards(
-            admin,
-            admin,
+            _admin,
+            _admin,
             govToken,
             _riskToken,
             _epochEnd,
```
src/rewards/StakingRewards.sol
1. No need to initialize state variable to 0
2. Use local variable to cache state variable.

```git diff
diff --git a/src/rewards/StakingRewards.sol b/src/rewards/StakingRewards.sol
index 5edb4e8..a0cb6ba 100644
--- a/src/rewards/StakingRewards.sol
+++ b/src/rewards/StakingRewards.sol
@@ -33,7 +33,8 @@ contract StakingRewards is
 
     ERC20 public immutable rewardsToken;
     IERC1155 public immutable stakingToken;
-    uint256 public periodFinish = 0;
+    //No need to initalize state variable to 0
+    uint256 public periodFinish;
     uint256 public rewardRate;
     uint256 public rewardsDuration;
     uint256 public lastUpdateTime;
@@ -96,14 +97,15 @@ contract StakingRewards is
         require(amount != 0, "Cannot stake 0");
         _totalSupply = _totalSupply.add(amount);
         _balances[msg.sender] = _balances[msg.sender].add(amount);
+        uint256 _id = id;
         stakingToken.safeTransferFrom(
             msg.sender,
             address(this),
-            id,
+            _id,
             amount,
             ""
         );
-        emit Staked(msg.sender, id, amount);
+        emit Staked(msg.sender, _id, amount);
     }
 
     function exit() external {
@@ -119,14 +121,15 @@ contract StakingRewards is
         require(amount > 0, "Cannot withdraw 0");
         _totalSupply = _totalSupply.sub(amount);
         _balances[msg.sender] = _balances[msg.sender].sub(amount);
+        uint256 _id = id;
         stakingToken.safeTransferFrom(
             address(this),
             msg.sender,
-            id,
+            _id,
             amount,
             ""
         );
-        emit Withdrawn(msg.sender, id, amount);
+        emit Withdrawn(msg.sender, _id, amount);
     }
 
     function getReward() public nonReentrant updateReward(msg.sender) {
```