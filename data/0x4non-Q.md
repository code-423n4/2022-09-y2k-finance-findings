# QA

Its more clear use named imports as you do on [Controller.sol#L4](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L4) (and in other contracts)

Change to this pattern to gain clarity on contracts imported
```solidity
import {Contract} from "/Contract.sol";
import {Contract1,Contract2,Contract3} from "/Contracts.sol";
```
On this lines;
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L5-L8
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsDistributionRecipient.sol#L4
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L4
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsDistributionRecipient.sol#L4
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L19
---

Use solmate WETH instead of creating a interface, it will be less code to audit and will give you a full WETH interface;

```diff
diff --git a/src/Vault.sol b/src/Vault.sol
index 1d2e6df..8bed90c 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -4,7 +4,7 @@ pragma solidity 0.8.15;
 import {SemiFungibleVault} from "./SemiFungibleVault.sol";
 import {ERC20} from "@solmate/tokens/ERC20.sol";
 import {FixedPointMathLib} from "@solmate/utils/FixedPointMathLib.sol";
-import {IWETH} from "./interfaces/IWETH.sol";
+import {WETH} from "@solmate/tokens/WETH.sol";
 import {
     ReentrancyGuard
 } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
@@ -186,8 +186,8 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
     {
         require(msg.value > 0, "ZeroValue");
 
-        IWETH(address(asset)).deposit{value: msg.value}();
-        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
+        WETH(address(asset)).deposit{value: msg.value}();
+        assert(WETH(address(asset)).transfer(msg.sender, msg.value));
 
         return deposit(id, msg.value, receiver);
     }
diff --git a/src/interfaces/IWETH.sol b/src/interfaces/IWETH.sol
deleted file mode 100644
index 7c24755..0000000
--- a/src/interfaces/IWETH.sol
+++ /dev/null
@@ -1,10 +0,0 @@
-// SPDX-License-Identifier: AGPL-3.0-only
-pragma solidity 0.8.15;
-
-interface IWETH {
-    function deposit() external payable;
-
-    function transfer(address to, uint256 value) external returns (bool);
-
-    function withdraw(uint256) external;
-}
```

---

Use underscore to make big numbers human readable, example in [`PegOracle.sol`](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol)
```diff
diff --git a/src/oracles/PegOracle.sol b/src/oracles/PegOracle.sol
index 1c65268..cd9a6e2 100644
--- a/src/oracles/PegOracle.sol
+++ b/src/oracles/PegOracle.sol
@@ -65,9 +65,9 @@ contract PegOracle {
         int256 price2 = getOracle2_Price();
 
         if (price1 > price2) {
-            nowPrice = (price2 * 10000) / price1;
+            nowPrice = (price2 * 10_000) / price1;
         } else {
-            nowPrice = (price1 * 10000) / price2;
+            nowPrice = (price1 * 10_000) / price2;
         }
 
         int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
@@ -75,7 +75,7 @@ contract PegOracle {
 
         return (
             roundID1,
-            nowPrice / 1000000,
+            nowPrice / 1_000_000,
             startedAt1,
             timeStamp1,
             answeredInRound1
```

---

No event emission on critical functions in [`Vault.sol#L277-L300`](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L277-L300). The functions `changeTreasury`, `changeTimewindow`, `changeController` and `createAssets` are critical, they should emit events to track the changes.
File: https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L277-L300

---

Use safeTransfer to transfer tokens on [Vault](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol)

```diff
diff --git a/src/Vault.sol b/src/Vault.sol
index 1d2e6df..6b665cf 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -8,9 +8,11 @@ import {IWETH} from "./interfaces/IWETH.sol";
 import {
     ReentrancyGuard
 } from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
+import {SafeTransferLib} from "@solmate/utils/SafeTransferLib.sol";
 
 contract Vault is SemiFungibleVault, ReentrancyGuard {
     using FixedPointMathLib for uint256;
+    using SafeTransferLib for ERC20;
 
     /*//////////////////////////////////////////////////////////////
                                  ERRORS
@@ -164,7 +166,7 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
         // Check for rounding error since we round down in previewDeposit.
         require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
 
-        asset.transferFrom(msg.sender, address(this), shares);
+        asset.safeTransferFrom(msg.sender, address(this), shares);
 
         _mint(receiver, id, shares, EMPTY);
 
@@ -187,7 +189,7 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
         require(msg.value > 0, "ZeroValue");
 
         IWETH(address(asset)).deposit{value: msg.value}();
-        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
+        asset.safeTransfer(msg.sender, msg.value);
 
         return deposit(id, msg.value, receiver);
     }
@@ -225,10 +227,10 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
         //Taking fee from the amount
         uint256 feeValue = calculateWithdrawalFeeValue(entitledShares, id);
         entitledShares = entitledShares - feeValue;
-        asset.transfer(treasury, feeValue);
+        asset.safeTransfer(treasury, feeValue);
 
         emit Withdraw(msg.sender, receiver, owner, id, assets, entitledShares);
-        asset.transfer(receiver, entitledShares);
+        asset.safeTransfer(receiver, entitledShares);
 
         return entitledShares;
     }
@@ -362,7 +364,7 @@ contract Vault is SemiFungibleVault, ReentrancyGuard {
         onlyController
         marketExists(id)
     {
-        asset.transfer(_counterparty, idFinalTVL[id]);
+        asset.safeTransfer(_counterparty, idFinalTVL[id]);
     }
 
     /*///////////////////////////////////////////////////////////////
```

---

Dont reimplement auth owner logic, instead of [Owned.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/Owned.sol) use soltmate `auth/Owned.sol` or openzepellin `Ownable` implementation

---

