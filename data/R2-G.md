## 1. Use ``entitledShares -= feeValue`` instead of  ``entitledShares = entitledShares - feeValue`` 

## 2. ``PegOracle.decimals`` is unused

## 3. No need to duplicate ``oracle1`` and ``priceFeed1`` in ``PegOracle``. Because 
```
priceFeed1 = AggregatorV3Interface(_oracle1)
```
