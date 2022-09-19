* functions ```getOracle1_Price``` and ```getOracle2_Price``` are too similar. They should be re-used with different parameters, e.g.:
```solidity
    function getOracle_Price(AggregatorV3Interface priceFeed) public view returns (int256 price) {
        (
        uint80 roundID,
        int256 price,
        ,
        uint256 timeStamp,
        uint80 answeredInRound
        ) = priceFeed.latestRoundData();

        require(price > 0, "Chainlink price <= 0");
        require(
            answeredInRound >= roundID,
            "RoundID from Oracle is outdated!"
        );
        require(timeStamp != 0, "Timestamp == 0 !");

        return price;
    }
```

* In ```PegOracle```, decimals are cached in the constructor:
```solidity
  uint8 public decimals;
  decimals = priceFeed1.decimals();
```
However, when fetching the price, the decimals are queried again:
```solidity
  int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
```

* No need to recalculate constant values every time the function is invoked, you should extract them to constant variables, 
e.g. ```keccak256(abi.encodePacked("rY2K"))``` never changes here:
```solidity
  if (
      keccak256(abi.encodePacked(symbol)) ==
      keccak256(abi.encodePacked("rY2K"))
  )
```

* Admin state variable and modifier ```onlyAdmin``` are never used in the Controller. Consider removing them to save some gas.

* Results of repeated calls should be cached and re-used, e.g. here it externally calls ```vaultFactory.getVaults(marketIndex)``` twice:
```solidity
  if(
  vaultFactory.getVaults(marketIndex).length != VAULTS_LENGTH)
      revert MarketDoesNotExist(marketIndex);
  ...
  address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);
```