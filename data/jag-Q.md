https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L192
controller is checked for 0 address later but its function is accessed before.
Patch to fix the issue.
```git diff
diff --git a/src/VaultFactory.sol b/src/VaultFactory.sol
index bfd70f1..4f48d17 100644
--- a/src/VaultFactory.sol
+++ b/src/VaultFactory.sol
@@ -184,14 +184,13 @@ contract VaultFactory {
         address _oracle,
         string memory _name
     ) public onlyAdmin returns (address insr, address rsk) {
+        if(controller == address(0))
+            revert ControllerNotSet();
         if(
             IController(controller).getVaultFactory() != address(this)
             )
             revert AddressFactoryNotInController();
 
-        if(controller == address(0))
-            revert ControllerNotSet();
-
         marketIndex += 1;
 
         //y2kUSDC_99*RISK or y2kUSDC_99*HEDGE
```