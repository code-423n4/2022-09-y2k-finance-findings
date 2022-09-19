# Low Priority / Non Critical issues

## [NC-01] Market existance should be verified by factory

Controller calls VaultFactory's getVaults() which returns the Vaults array, and then check wether the array length is 2. This is repeated/duplicated in both triggerDepeg and triggerEndEpoch wich violates DRY. It also violates responsibility principle, as it is VaultFactory concern to return a correct response if the preconditions exist.

Suggested changes follow.

File-Line: [VaultFactory.sol:L385-L391](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L385-L391)
```solidity
     function getVaults(uint256 index)
         public
         view
-        returns (address[] memory vaults)
+        returns (address[2] memory vaults)
     {
-        return indexVaults[index];
+        vaults = indexVaults[index];
+        if(vaults[0] == address(0) || vaults[1] == address(0))
+            revert MarketDoesNotExist(index);
+
     }
 }
```
 > **_NOTE:_** the code above also includes a gas optimization described [here](#vaultfactorysol-can-use-fixed-size-array-for-market-vaults).

File-Line: [Controller.sol:L83-L88](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L83-L88)
```solidity
     modifier isDisaster(uint256 marketIndex, uint256 epochEnd) {
         address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
-        if(
-            vaultsAddress.length != VAULTS_LENGTH
-            )
-            revert MarketDoesNotExist(marketIndex);
```

File-Line: [Controller.sol:L198-L201](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L198-L201)
```solidity
     function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
-        if(
-            vaultFactory.getVaults(marketIndex).length != VAULTS_LENGTH)
-                revert MarketDoesNotExist(marketIndex);
```

## [NC-02] Controller address should be verified before using it

In VaultFactory::createNewMarket the controller address is tested for zero address after using it to test vault factory address. Move test for zero address on top.

File-Line: [VaultFactory.sol:L188-L192](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L188-L192)

Actual
```solidity
if(
    IController(controller).getVaultFactory() != address(this)
    )
    revert AddressFactoryNotInController();

if(controller == address(0))
    revert ControllerNotSet();
```

Expected
```solidity
if(controller == address(0))
    revert ControllerNotSet();

if(
    IController(controller).getVaultFactory() != address(this)
    )
    revert AddressFactoryNotInController();

```

## [NC-03] timewindow has no bounds

File-Line: [Vault.sol:L287-L289](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L287-L289)

By default timewindow is set to 1 day, but it can be set to any unsigned value. The changeTimewindow should validate the new timewindow for valid bounds.

```solidity
    function changeTimewindow(uint256 _timewindow) public onlyFactory {
+       if(_timeWindow < 1 hours || _timewindow > 2 days) // Define valid bounds
+           revert InvalidTimewindow(_timewindow);
        timewindow = _timewindow;
    }
```

## [NC-04] Vault's withdraw can be called after epoch expires, before end is triggered

File-Line: [Vault.sol:L211](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L211)

modifier `epochHasEnded` should eventually be called `epochHasExpired`, because it does not catch wether Controller's triggerEndEpoch was called or not.
Since it is not possible to withdraw without first executing either triggerEndEpoch or triggerDepegged, an extra validation needs to be added to verify trigger execution, which in this case can be done by checking idFinalTVL[id] state. This prevents the withdraw from reverting for dividing by zero and instead return a meaningful error.

```solidity
    modifier epochHasEnded(uint256 id) {
-       if((block.timestamp < id) && idDepegged[id] == false)
+       if(((block.timestamp < id) && idDepegged[id] == false) || idFinalTVL[id] == 0)
            revert EpochNotFinished();
        _;
    }
```

## [NC-05] Errors should help to quickly identify the root cause or security issues

File: [Controller.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol)

[Line 75](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L75)
```solidity
-revert AddressNotAdmin();
+revert AddressNotAdmin(msg.sender);
```

[Line 94](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L94), [Line 212](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L212)
```solidity
-revert EpochNotExist();
+revert EpochNotExist(epochEnd);
```

[Line 103](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L103)
```solidity
-revert EpochNotStarted();
+revert EpochNotStarted(block.timestamp, epochEnd);
```

[Line 108](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L108)
```solidity
-revert EpochExpired();
+revert EpochExpired(block.timestamp, epochEnd);
```

[Line 158](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L158), [Line 160](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L160), [Line 216](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L216), [Line 218](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L218)
```solidity
-revert NotZeroTVL();
+revert NotZeroTVL(epochEnd);
```

[Line 204](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L204)
```solidity
-revert EpochNotExpired();
+revert EpochNotExpired(block.timestamp, epochEnd);
```

[Line 285](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L285)
```solidity
-revert GracePeriodNotOver();
+revert GracePeriodNotOver(block.timestamp, timeSinceUp, GRACE_PERIOD_TIME);
```

[Line 306](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L306)
```solidity
-revert RoundIDOutdated();
+revert RoundIDOutdated(block.timestamp, answeredInRound, roundID);
```

[Line 309](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L309)
```solidity
-revert TimestampZero();
+revert TimestampZero(block.timestamp);
```

File: [VaultFactory.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol)

[Line 190](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L190)
```solidity
-revert AddressFactoryNotInController();
+revert AddressFactoryNotInController(controller, vaultFactory /*cached getVaultFactory()*/, address(this));
```

File: [Vault.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol)

[Line 81](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L81)
```solidity
-revert MarketEpochDoesNotExist();
+revert MarketEpochDoesNotExist(id);
```

[Line 89](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L89)
```solidity
-revert EpochAlreadyStarted();
+revert EpochAlreadyStarted(block.timestamp, idEpochBegin[id], timewindow);
```

[Line 97](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L97)
```solidity
-revert EpochNotFinished();
+revert EpochNotFinished(block.timestamp, id);
```

[Line 315](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L315)
```solidity
-revert MarketEpochExists();
+revert MarketEpochExists(epochEnd);
```

[Line 318](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L318)
```solidity
-revert EpochEndMustBeAfterBegin();
+revert EpochEndMustBeAfterBegin(epochBegin, epochEnd);
```

## [NC-06] nonReentrant modifier should be on top of other modifiers

File-Line: [Vault.sol:L161](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L161)

As a best-practice always set the nonReentrant modifier on top to avoid reentracy on other modifiers.

```solidity
    function deposit(
        uint256 id,
        uint256 assets,
        address receiver
    )
        public
        override
+       nonReentrant        
        marketExists(id)
        epochHasNotStarted(id)
-       nonReentrant
        returns (uint256 shares)
    {
```