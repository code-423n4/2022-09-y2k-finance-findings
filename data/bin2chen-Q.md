1. Controller.sol

When block.timestamp = epochEnd , and latestPrice < strikePrice 
triggerDepeg() can pass and triggerEndEpoch()  also can pass, so Keeper can choose trigger "Depeg" or "not Depeg" 
triggerDepeg() recommended to use >=epochEnd 

```
    modifier isDisaster(uint256 marketIndex, uint256 epochEnd) {
    ...
        if(
---          block.timestamp > epochEnd
+++          block.timestamp >= epochEnd
            )
            revert EpochExpired();
        _;    
```

2. Vault.sol
createAssets() add validate the legality of epochBegin, avoid failing to deposit
```
    function createAssets(uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee)
        public
        onlyFactory
    {

...
        if(epochBegin >= epochEnd)
            revert EpochEndMustBeAfterBegin();

+++     require(epochBegin >= block.timestamp  + timewindow * 2); 	


    }
```

3.Vault.sol
withdraw() adds verification of the legitimacy of receive
```
    function withdraw(
        uint256 id,
        uint256 assets,
        address receiver,
        address owner
    )
        external
        override
        epochHasEnded(id)
        marketExists(id)
        returns (uint256 shares)
    {

+++     if(receiver == address(0))
+++          revert AddressZero(); 

```

4.Controller.sol
isDisaster() adds temporary variable to save getLatestPrice() to reduce gas consumption
```
    modifier isDisaster(uint256 marketIndex, uint256 epochEnd) {
    ...

+++     int256 lastPrice = getLatestPrice(vault.tokenInsured();
        if(
---         vault.strikePrice() < getLatestPrice(vault.tokenInsured())
+++         vault.strikePrice() < lastPrice)
            )
---         revert PriceNotAtStrikePrice(getLatestPrice(vault.tokenInsured()));
+++         revert PriceNotAtStrikePrice(lastPrice);
```



