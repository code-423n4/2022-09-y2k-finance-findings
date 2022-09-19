# QA

### Unsafe ERC20 Operation(s)

### Examples:
```
2022-09-y2k-finance\src\Vault.sol::167 => asset.transferFrom(msg.sender, address(this), shares);
2022-09-y2k-finance\src\Vault.sol::190 => assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
2022-09-y2k-finance\src\Vault.sol::228 => asset.transfer(treasury, feeValue);
2022-09-y2k-finance\src\Vault.sol::231 => asset.transfer(receiver, entitledShares);
2022-09-y2k-finance\src\Vault.sol::365 => asset.transfer(_counterparty, idFinalTVL[id]);
```