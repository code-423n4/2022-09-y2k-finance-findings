## Low severtity findings
### 1. `pegOracle` assumes the oracle feeds provided have 8 decimals.
 While this is true as of now for chainlink USD feeds this could change in the future for new feeds and could lead to incorrect calculations in [`PegOracle.sol#L67-L82`](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L67-L82). Also powers of 10 are used as literals in calculations. Consider normalizing to decimals provided:
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L67-L82

```diff
        if (price1 > price2) {  
-           nowPrice = (price2 * 10000) / price1;
+           nowPrice = (price2 * 10**decimals) / price1;
        } else {
-           nowPrice = (price1 * 10000) / price2;
+           nowPrice = (price1 * 10**decimals) / price2;
        }  

-       int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));  
-       nowPrice = nowPrice * decimals10;  
        return (
            roundID1,
-           nowPrice / 1000000,  
+           nowPrice,
            startedAt1,
            timeStamp1,
            answeredInRound1
        );
```
### 2. `Vault` does not respect the ERC4626 standard as claimed. 
In `Vault`, the `withdraw` function allows to withdraw an amount of underlying that is different from parameter `assets`. As a counsequence, `previewWithdraw` does not actually work as expected as wall as the `convert` functions. Also there is confusion in that function around whether the sender withdraws assets or shares since at the end we find `asset.transfer(receiver, entitledShares)`. This issue could cause intergation problems since the Valut is presented as a standard ERC4626 while it is not (for example one could expect to have a withdrawal as given by `previewWithdraw` while this is not the case)

### 3. Avoid using reverting modifiers in view functions
See [Vault.sol#L248](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L248). A view function is generally not expected to revert.
 
 
## Non critical findings
### 1. Inconsistent usage of custom errors:
Custom errors and revert strings are both used and sometimes a revert string is used when a custom error exists for that (see[Vault.sol#L165](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L165)). Consider using using only custom errors or at least use a custom error when it already exists.

### 2. Redundant or unreachable code:
-   In `SemiFulngibleVault.sol` the functions `withdraw` and `deposit` are overridden by their implementation in `Valut.sol`. Consider just leaving them as non implemented in `SemiFulngibleVault.sol`.
-   In [Vault.sol#L190](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L190) the assert statement never throws since `WETH9` can never return `false` but instead reverts on failure.
-  In [VaultFactory.sol#L192-L193](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L192-L193) the revert statement is unreachable since if the `controller` is `address(0)` then the previous check would revert. Consider moving the `if(controller == address(0))` before the `if(IController(controller).getVaultFactory() != address(this))` check.

### 3. Incomplete or inaccurate Natspec comments
Some public and external functions have incomplete Natspec Comments (missing parameters):
- [SemiFungibleVault.sol#L234-L270](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L234-L270)  

Some parameters are missing: `strikePrice` at [VaultFactory.sol#L47](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L47) and `withdrawalFee` at [VaultFactory.sol#L67](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L67)

Some comments are inaccurate: 
- [Vault.sol#L85](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L85)
- [Vault.sol#L93](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L93)
- [SemiFungibleVault.sol#L261](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L261)

### 4. incorrect parameter passed when trasnferring asset from depositor
In [Vault.sol#L167](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L167) `assets` should be used instead of `shares`. This is not an issue here since the vault has a constant 1:1 ratio.

### 5.use `mulWadDown` instead of `mulDivDown` and 1 Ether as denominator
In [Vault.sol#L387-L412](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L387-L412) consider simplyfing the code by using `amount.divWadDown(idFinalTVL[id]).mulWadDown(idClaimTVL[id])` instead of `amount.divWadDown(idFinalTVL[id]).mulDivDown(idClaimTVL[id],1 ether)`
