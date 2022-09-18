### RETURN VALUES OF `TRANSFER()` and `TRANSFERFROM` NOT CHECKED
 Some implementations of `transfer` and `transferFrom` could return ‘false’ on failure instead of reverting. It is safer to wrap such calls into `require()` statements to these failures.
```solidity
Vault.sol::167 => asset.transferFrom(msg.sender, address(this), shares);
Vault.sol::228 => asset.transfer(treasury, feeValue);
Vault.sol::231 => asset.transfer(receiver, entitledShares);
Vault.sol::365 => asset.transfer(_counterparty, idFinalTVL[id]);
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L167
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L228
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L231
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L365
&nbsp;
&nbsp;
## `REQUIRE()` SHOULD BE USED INSTEAD OF `ASSERT()`
Hitting an assert consumes the **remainder of the transaction’s available gas** rather than returning it, as `require()`/`revert()` do
```solidity
Vault.sol::190 => assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190
&nbsp;
&nbsp;
## `_SAFEMINT()` SHOULD BE USED RATHER THAN `_MINT()` WHEREVER POSSIBLE
```solidity
Vault.sol::169 =>         _mint(receiver, id, shares, EMPTY);
SemiFungibleVault.sol::96 =>         _mint(receiver, id, shares, EMPTY);
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L169
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L96