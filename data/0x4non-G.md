# GAS

## Remove dead code from `Controller.sol`

You are not using Controller admin or onlyAdmin modifier, remove this logic to save gas an audit lines.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol
```diff
diff --git a/src/Controller.sol b/src/Controller.sol
index e15b0fa..47c66d9 100644
--- a/src/Controller.sol
+++ b/src/Controller.sol
@@ -8,7 +8,6 @@ import "@chainlink/interfaces/AggregatorV3Interface.sol";
 import "@chainlink/interfaces/AggregatorV2V3Interface.sol";
 
 contract Controller {
-    address public immutable admin;
     VaultFactory public immutable vaultFactory;
     AggregatorV2V3Interface internal sequencerUptimeFeed;
 
@@ -64,17 +63,6 @@ contract Controller {
     }
     /* solhint-enable  var-name-mixedcase */
 
-    /*//////////////////////////////////////////////////////////////
-                                 MODIFIERS
-    //////////////////////////////////////////////////////////////*/
-
-    /** @notice Only admin addresses can call functions that use this modifier
-      */
-    modifier onlyAdmin() {
-        if(msg.sender != admin)
-            revert AddressNotAdmin();
-        _;
-    }
 
     /** @notice Modifier to ensure market exists, current market epoch time and price are valid 
       * @param marketIndex Target market index
@@ -115,12 +103,10 @@ contract Controller {
 
     /** @notice Contract constructor
       * @param _factory VaultFactory address
-      * @param _admin Admin address
       * @param _l2Sequencer Arbitrum sequencer address
       */ 
     constructor(
         address _factory,
-        address _admin,
         address _l2Sequencer
     ) {
         if(_admin == address(0))
@@ -132,7 +118,6 @@ contract Controller {
         if(_l2Sequencer == address(0))
             revert ZeroAddress();
         
-        admin = _admin;
         vaultFactory = VaultFactory(_factory);
         sequencerUptimeFeed = AggregatorV2V3Interface(_l2Sequencer);
     }
@@ -247,10 +232,6 @@ contract Controller {
         );
     }
 
-    /*//////////////////////////////////////////////////////////////
-                                ADMIN SETTINGS
-    //////////////////////////////////////////////////////////////*/
-
     /*//////////////////////////////////////////////////////////////
                                 GETTERS
     //////////////////////////////////////////////////////////////*/

```

---

## Some state variables in `PegOracle.sol` should be immutable
The variables `oracle1`, `oracle2`, `decimals`, `priceFeed1` and `priceFeed2` never change, they could be immutable to save gas.

File https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol
```diff
diff --git a/src/oracles/PegOracle.sol b/src/oracles/PegOracle.sol
index 1c65268..fa2884b 100644
--- a/src/oracles/PegOracle.sol
+++ b/src/oracles/PegOracle.sol
@@ -7,13 +7,13 @@ contract PegOracle {
     /***
     @dev  for example: oracle1 would be stETH / USD, while oracle2 would be ETH / USD oracle
     ***/
-    address public oracle1;
-    address public oracle2;
+    address public immutable oracle1;
+    address public immutable oracle2;
 
-    uint8 public decimals;
+    uint8 public immutable decimals;
 
-    AggregatorV3Interface internal priceFeed1;
-    AggregatorV3Interface internal priceFeed2;
+    AggregatorV3Interface internal immutable priceFeed1;
+    AggregatorV3Interface internal immutable priceFeed2;
 
     /** @notice Contract constructor
       * @param _oracle1 First oracle address
}
```

---

## Cache decimals on `PegOracle.sol` to avoid extra call

File https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol
```diff
diff --git a/src/oracles/PegOracle.sol b/src/oracles/PegOracle.sol
index 1c65268..cb83254 100644
--- a/src/oracles/PegOracle.sol
+++ b/src/oracles/PegOracle.sol
@@ -19,21 +19,23 @@ contract PegOracle {
       * @param _oracle1 First oracle address
       * @param _oracle2 Second oracle address
       */
-    constructor(address _oracle1, address _oracle2) {
+        constructor(address _oracle1, address _oracle2) {
         require(_oracle1 != address(0), "oracle1 cannot be the zero address");
         require(_oracle2 != address(0), "oracle2 cannot be the zero address");
         require(_oracle1 != _oracle2, "Cannot be same Oracle");
         priceFeed1 = AggregatorV3Interface(_oracle1);
         priceFeed2 = AggregatorV3Interface(_oracle2);
+
+        uint8 _decimals = priceFeed1.decimals();
         require(
-            (priceFeed1.decimals() == priceFeed2.decimals()),
+            (_decimals == priceFeed2.decimals()),
             "Decimals must be the same"
         );
 
         oracle1 = _oracle1;
         oracle2 = _oracle2;
 
-        decimals = priceFeed1.decimals();
+        decimals = _decimals;
     }
```

---

## Use internal variable `decimals` instead of external call on `PegOracle.sol`

File https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol
```diff
diff --git a/src/oracles/PegOracle.sol b/src/oracles/PegOracle.sol
index 1c65268..855e4d2 100644
--- a/src/oracles/PegOracle.sol
+++ b/src/oracles/PegOracle.sol
@@ -70,7 +70,7 @@ contract PegOracle {
             nowPrice = (price1 * 10000) / price2;
         }
 
-        int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
+        int256 decimals10 = int256(10**(18 - decimals));
         nowPrice = nowPrice * decimals10;
 
         return (
```

---

## Use revert messages less than 32 bytes or use custom errors to save gas

On [PegOracle.sol#L101](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L101), [PegOracle.sol#L124](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L124), [Owned.sol#L30](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/Owned.sol#L30) and [Owned.sol#L40](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/Owned.sol#L40) the revert message is greater than 32 bytes, use a custom error or a shorter message
* [custom errors doc](https://blog.soliditylang.org/2021/04/21/custom-errors/)

---

## Use solmate reentrancy guard instead of openzeppelin
```diff
diff --git a/src/Vault.sol b/src/Vault.sol
index 1d2e6df..b854850 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -7,7 +7,7 @@ import {FixedPointMathLib} from "@solmate/utils/FixedPointMathLib.sol";
 import {IWETH} from "./interfaces/IWETH.sol";
 import {
     ReentrancyGuard
-} from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
+} from "@solmate/utils/ReentrancyGuard.sol";
 
 contract Vault is SemiFungibleVault, ReentrancyGuard {
     using FixedPointMathLib for uint256;
diff --git a/src/rewards/StakingRewards.sol b/src/rewards/StakingRewards.sol
index 5edb4e8..ab36fb7 100644
--- a/src/rewards/StakingRewards.sol
+++ b/src/rewards/StakingRewards.sol
@@ -5,7 +5,7 @@ import {SafeMath} from "@openzeppelin/contracts/utils/math/SafeMath.sol";
 import {SafeTransferLib} from "@solmate/utils/SafeTransferLib.sol";
 import {
     ReentrancyGuard
-} from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
+} from "@solmate/utils/ReentrancyGuard.sol";
 
 // Inheritance
 import {IStakingRewards} from "./IStakingRewards.sol";
```

---
## unnecesary use of reentrancyGuard

There is no reentrancy issue on;
[StakingRewards.sol#L92](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L92)
[StakingRewards.sol#L116](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L116)
[StakingRewards.sol#L132](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L132)

You could safely remove this modifier and reentrancy guard import.

---
## Unnecesary use of `SafeMath`
Solidity v0.8.0 introduces built-in overflow checks, so using SafeMath is a waste of gas.

Remove SafeMath from [StakingRewards.sol#L29](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L29)

---

## Cache length on loops
On [Vault.sol#L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443)

```diff
diff --git a/src/Vault.sol b/src/Vault.sol
index 1d2e6df..0bd4b31 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -440,9 +440,10 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
         view
         returns (uint256 nextEpochEnd)
     {
-        for (uint256 i = 0; i < epochsLength(); i++) {
+        uint256 _length = epochs.length;
+        for (uint256 i = 0; i < _length; i++) {
             if (epochs[i] == _epoch) {
-                if (i == epochsLength() - 1) {
+                if (i == _length - 1) {
                     return 0;
                 }
                 return epochs[i + 1];
```

##  Prefix increments are cheaper than postfix increments use `unchecked { ++i; }` on loop
On [Vault.sol#L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443) use this for loops;
```diff
diff --git a/src/Vault.sol b/src/Vault.sol
index 1d2e6df..e175426 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -440,13 +440,16 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
         view
         returns (uint256 nextEpochEnd)
     {
-        for (uint256 i = 0; i < epochsLength(); i++) {
+        for (uint256 i = 0; i < epochsLength();) {
             if (epochs[i] == _epoch) {
                 if (i == epochsLength() - 1) {
                     return 0;
                 }
                 return epochs[i + 1];
             }
+            unchecked {
+                ++i;
+            }
         }
     }
 }
```
