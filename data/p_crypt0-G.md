# Vault.sol

## Use if statements over Require assertion

#### deposit()
Line: 165

````
require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
````

Should be:
````
error ZeroValue();
…
if ((shares=previewDeposit(id, assets)) == 0, revert ZeroValue();
````

The error, ZeroValue() exists on line 25, but not implemented.


#### depositETH()
Line 187

````
require(msg.value > 0, "ZeroValue");
````

Should be
````
error ZeroValue();
…
If (msg.value == 0) revert ZeroValue();
````

# SemiFungibleVault.sol
## Use if statement over Require assertion

#### deposit()

Line 91:
````

        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
````
Should be:
````
error ZeroShares();

        if ((shares = previewDeposit(id, assets)) == 0) revert ZeroShares();
````

#### withdraw()

Line 116:
````

 require(
            msg.sender == owner || isApprovedForAll(owner, receiver),
            "Only owner can withdraw, or owner has approved receiver for all"
        );
````

Should be:

````
error NotOwner();

if (msg.sender != owner || !isApprovedForAll(owner, receiver)) revert NotOwner();
````

# PegOracle.sol

## Use if statement over Require assertion

#### constructor()
Line 23 - 25:
````

       

	require(_oracle1 != address(0), "oracle1 cannot be the zero address");
	require(_oracle2 != address(0), "oracle2 cannot be the zero address"); 
	require(_oracle1 != _oracle2, "Cannot be same Oracle");
````
Should be:
````
error ZeroAddress();
error SameOracle();
…

if (_oracle1 == address(0)) revert ZeroAddress();
If(_oracle2 == address(0)) revert ZeroAddress();
If (_oracle1 == _oracle2) revert SameOracle();

````
Line 28 - 31

````

 require(
            (priceFeed1.decimals() == priceFeed2.decimals()),
            "Decimals must be the same"
        );

````

Should be:

````
error DecimalsNotSame();


if (priceFeed1.decimals() != priceFeed2.decimals()) revert DecimalsNotSame();
````
#### getOracle1_Price()
Line 98

````

        require(price1 > 0, "Chainlink price <= 0");


````

Should be

````
Error ChainlinkPriceBelowZero()

if ( price1 <= 0 ) revert ChainlinkPriceBelowZero();
````

Line 99

````

require(
            answeredInRound1 >= roundID1,
            "RoundID from Oracle is outdated!"
        );

````

Should be:

````
error RoundIdOutdated();

If ( answeredInRound1 < roundID1 ) revert RoundIdOutdated();
````

Line 103
````
require(timestamp1 != 0, "Timestamp == 0");
````
Should be:
````
error LandBeforeTime();
if (timestamp1 == 0) revert LandBeforeTime();
````

#### getOracle2_Price()

Line 121:

````
require (price2 > 0), "Chainlink price  <= 0");
````
Should be:
````
if (price2 <= 0) revert ChainlinkPriceBelowZero();
````

Line 122:

````
require (answeredInRound2 >= roundID2, "RoundID from Oracle is outdated!");
````

Should be:

````
If (answeredInRound2 < roundID2) revert RoundIdOutdated();
````

Line 126:

````
Require(timestamp2 != 0, "Timestamp == 0 !");
````
Should be:
````
if (timestamp2 == 0) revert LandBeforeTime();
````
