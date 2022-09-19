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
