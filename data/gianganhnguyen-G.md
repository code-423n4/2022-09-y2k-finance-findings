# 1. [G-1]  ++i  cost less gas compare i++ in for loops

File Vault.sol, line 443:

    for (uint256 i = 0; i < epochsLength(); i++) {
       ...
    }

Becomes:

    for (uint256 i = 0; i < epochsLength(); ++i) {
        ...
    }

# 2. [G-2] Cache the length of arrays in the loops

File Vault.sol, line 443:

    for (uint256 i = 0; i < epochsLength(); i++) {
       ...
    }

Becomes:

    uint256 epochsLength = epochsLength();
    for (uint256 i = 0; i < epochsLength; ++i) {
       ...
    }

# 3. [G-3] Using uncheck blocks to save gas - increments in for loop can be uncheck

File Vault.sol, line 443:

    for (uint256 i = 0; i < epochsLength(); i++) {
       ...
    }

Becomes:

    uint256 epochsLength = epochsLength();
    for (uint256 i = 0; i < epochsLength ;) {
          ...
          unchecked { ++i; }
      }

# 4. [G-4] Initialize uint256 <variable>; is cheaper than uint256 <variable> = 0

File Vault.sol, line 443:

    for (uint256 i = 0; i < epochsLength(); i++) {
       ...
    }

Becomes:

    for (uint256 i; i < epochsLength(); i++) {
          ...
      }

File StakingRewards.sol, line 36:

    uint256 public periodFinish = 0;

Becomes:

    uint256 public periodFinish;

# 5. [G-5] Using uncheck blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

 File PegOracle.sol, line 67-74:

    if (price1 > price2) {
       nowPrice = (price2 * 10000) / price1;
    } else {
       nowPrice = (price1 * 10000) / price2;
    }

    int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
    nowPrice = nowPrice * decimals10;

Becomes:

    unchecked {
      if (price1 > price2) {
         nowPrice = (price2 * 10000) / price1;
      } else {
         nowPrice = (price1 * 10000) / price2;
      }

      int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
      nowPrice = nowPrice * decimals10;
    }

File Controller.sol, line 277:

    bool isSequencerUp = answer == 0;

Becomes:

    bool isSequencerUp;
    unchecked {
      isSequencerUp = answer == 0;
    }

File Controller.sol, line 283:

    uint256 timeSinceUp = block.timestamp - startedAt;

Becomes:

    uint256 timeSinceUp;
    unchecked {
      timeSinceUp = block.timestamp - startedAt;
    }

File Controller.sol, line 299, 300:

    uint256 decimals = 10**(18-(priceFeed.decimals()));
    price = price * int256(decimals);

Becomes:

    uint256 decimals;
    unchecked {
      decimals = 10**(18-(priceFeed.decimals()));
      price = price * int256(decimals);
    }

File Vault.sol, line 266:

    return (amount * epochFee[_epoch]) / 1000;

Becomes:

    unchecked {
      return (amount * epochFee[_epoch]) / 1000;
    }

##### Instances include:

File Vault.sol, line 399, 407, 419

# 6. [G-6] Using temporary memory 

File Controller.sol, line 96-99:

    if(
       vault.strikePrice() < getLatestPrice(vault.tokenInsured())
       )
       revert PriceNotAtStrikePrice(getLatestPrice(vault.tokenInsured()));

Becomes:

    int256 nowPrice = getLatestPrice(vault.tokenInsured());
    if(
       vault.strikePrice() < nowPrice
       )
       revert PriceNotAtStrikePrice(nowPrice);

File Controller.sol, line 199-206:

    if(
       vaultFactory.getVaults(marketIndex).length != VAULTS_LENGTH)
           revert MarketDoesNotExist(marketIndex);
    if(
       block.timestamp < epochEnd)
       revert EpochNotExpired();

    address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);

Becomes:

    address[] memory vaultsAddress = vaultFactory.getVaults(marketIndex);

    if(
       vaultsAddress.length != VAULTS_LENGTH)
           revert MarketDoesNotExist(marketIndex);
    if(
       block.timestamp < epochEnd)
       revert EpochNotExpired();
