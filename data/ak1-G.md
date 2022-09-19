1. Get value once and use it wherever needed.
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L96-L99
        ```if(
            vault.strikePrice() < getLatestPrice(vault.tokenInsured())
            )
            revert PriceNotAtStrikePrice(getLatestPrice(vault.tokenInsured()));```

call the `getLatestPrice(vault.tokenInsured()` once and use the result value in other places.

2. No need to initialize as zero in constructor. The default value itself is zero.
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L159

