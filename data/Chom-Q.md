## Use string.concat instead of string(abi.encodePacked

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L199-L217

```
        Vault hedge = new Vault(
            WETH,
            string(abi.encodePacked(_name,"HEDGE")),
            "hY2K",
            treasury,
            _token,
            _strikePrice,
            controller
        );

        Vault risk = new Vault(
            WETH,
            string(abi.encodePacked(_name,"RISK")),
            "rY2K",
            treasury,
            _token,
            _strikePrice,
            controller
        );
```

Should be changed to

```
        Vault hedge = new Vault(
            WETH,
            string.concat(_name,"HEDGE"),
            "hY2K",
            treasury,
            _token,
            _strikePrice,
            controller
        );

        Vault risk = new Vault(
            WETH,
            string.concat(_name,"RISK"),
            "rY2K",
            treasury,
            _token,
            _strikePrice,
            controller
        );
```

## Use safeTransfer instead of transfer to handle non-standard tokens such as USDT and the return value of the transfer is not checked

USDT doesn't return any boolean success on transfer

Vault.sol

```
167: asset.transferFrom(msg.sender, address(this), shares);
228: asset.transfer(treasury, feeValue);
231: asset.transfer(receiver, entitledShares);
365: asset.transfer(_counterparty, idFinalTVL[id]);
```

Should use safeTransfer / safeTransferFrom instead to handle optional return and checking of boolean success.