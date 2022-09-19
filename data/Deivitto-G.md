# GAS
## Functions can be simplified into just one
### Impact
Both functions can be simplified into just one function, with address parametrized. Also this can be achieved creating an internal function that runs with the logic and keeping the public functions such as:

```
    function getOracle1_Price() public view returns (int256 price) {
      price = _getOracle_Price(priceFeed1);
    }

    function getOracle2_Price() public view returns (int256 price) {
      price = _getOracle_Price(priceFeed2);
    }
```

and `_getOracle_Price` such as

```
function _getOracle_Price(address priceFeed) internal view returns (int256 price) {
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
```

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L89-L129

### Mitigation
Consider using parameters rather than deployment the same system twice

## Don't use `SafeMath` once the solidity version is `0.8.0` or greater
### Summary
Version `0.8.0` introduces internal overflow checks, so using `SafeMath` is redundant and adds overhead
### Github Permalink
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L4
import {SafeMath} from "@openzeppelin/contracts/utils/math/SafeMath.sol";

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L29
    using SafeMath for uint256;//@audit safemath after 0.8.0
### Recommendation
Remove `SafeMath` for gas optimization

## Public function visibility can be made external
### Summary
Functions should have the strictest visibility possible. Public functions may lead to more gas usage by forcing the copy of their parameters to memory from calldata.
### Details
If a function is never called from the contract it should be marked as external. This will save gas.
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L350-L352
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L287-L289
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L307-L325
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L295-L299
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L438-L451
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L336-L343
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L277-L281
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L360-L366
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L327-L338
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L345-L359
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L178-L239
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L366-L374
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L248-L266
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L385-L391
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L295-L301
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L308-L320
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L189-L198
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L237-L239
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L251-L258
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L244-L246
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L221-L228
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L263-L270
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L145-L151
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L89-L106
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L46-L83
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L198-L248
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L148-L192

### Mitigation
Consider changing visibility from public to external.

## use of custom errors rather than revert() / require() error message
### Summary
Custom errors reduce 38 gas if the condition is met and 22 gas otherwise.
Also reduces contract size and deployment costs.
### Details
Since version 0.8.4 the use of custom errors rather than revert() / require() saves gas as noticed in
https://blog.soliditylang.org/2021/04/21/custom-errors/
https://github.com/code-423n4/2022-04-pooltogether-findings/issues/13

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L91
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L116
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L165
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L187
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L23
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L24
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L25
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L28
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L98
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L99
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L103
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L121
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L122
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L126
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L96
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L119
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L202
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L217
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L226
### Mitigation
replace each error message in a require by a custom error

## duplicated require() check should be refactored
### Summary
duplicated require() / revert() checks should be
refactored to a modifier or function to save gas
### Details
Event appears twice and can be reduced

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L165
        require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L187
        require(msg.value > 0, "ZeroValue");
- - - -
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L23
        require(_oracle1 != address(0), "oracle1 cannot be the zero address");

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L24
        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
- - -
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L126
        require(timeStamp2 != 0, "Timestamp == 0 !");
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L103
        require(timeStamp1 != 0, "Timestamp == 0 !");       
- - -
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L98
        require(price1 > 0, "Chainlink price <= 0");
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L121
        require(price2 > 0, "Chainlink price <= 0");
- - -
### Mitigation
refactor this checks to different functions to save gas

## use != rather than >0 for unsigned integers in require() statements
### Summary
When the optimizer is enabled, gas is wasted by doing a greater-than operation, rather than a not-equals operation inside require() statements. When Using != , the optimizer is able to avoid the EQ, ISZERO, and associated operations, by relying on the JUMPI that comes afterwards, which itself checks for zero.
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L119
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L187
### Mitigation
Use != 0 rather than > 0 for unsigned integers in require()  statements.


## Reduce the size of error messages (Long revert Strings)
### Summary
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Github Permalinks

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L23

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L24

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L219

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L228

### Mitigation
Consider shortening the revert strings to fit in 32 bytes

## usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
### Summary
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

### Details
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 
Use a larger size than downcast where needed

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L13

### Mitigation
Consider using some data type that uses 32 bytes, for example uint256


## Store using Struct over multiple mappings
### Summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### Details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;
Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L47-L55
- - - 
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L119-L120
- - -
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L43-L44
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L47

### Mitigation
Consider mixing different mappings into an struct when able in order to save gas.

## Pack structs tightly
### Summary
Gas efficiency can be achieved by tightly packing the struct. Struct variables are stored in 32 bytes each so you can group smaller types to occupy less storage. 

### Details
You can read more here: https://fravoll.github.io/solidity-patterns/tight_variable_packing.html or in the official documentation: https://docs.soliditylang.org/en/v0.4.21/miscellaneous.html
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L20-L25

### Mitigation
Order structs to reduce gas usage.

## Pack state variables tightly
### Summary
State variables are expected to be ordered by data type, this helps readability and also gas optimization by tightly packing the variables.
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L36-L46
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L34-L38
### Mitigation
Order in a proper way the state variables to improve readability and to reduce gas usage.

## Using private rather than public for constants saves gas
### Summary
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L16

### Mitigation
Consider replacing public for private in constants for gas saving.

## Explicit initialization
### Summary
It is not needed to initialize variables to the default value. Explicitly initializing it with its default value is an anti-pattern and wastes gas.
### Details
If a variable is not set/initialized, it is assumed to have the default value ( 0 for uint, false for bool, address(0) for address…). 

### Github Permalinks

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L36
### Mitigation
Don't initialize variables to default value

## Index initialized in for loop
### Summary
In for loops is not needed to initialize indexes to 0 as it is the default uint/int value. This saves gas.

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L443

### Mitigation
Don't initialize variables to default value


## use of i++ in loop rather than ++i
### Summary
++i costs less gas than i++, especially when it's used in for loops as pre-increment is cheaper (about 5 gas per iteration). 
### Details
using ++i doesn't affect the flow of regular for loops and improves gas cost

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L443

### Mitigation
Substitute to ++i 

## ++i costs less gas compared to i++ or i+=1
### Summary
++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). 
This statement is true even with the optimizer enabled. 

### Details
i++ increments i and returns the initial value of i . 
Which means:
uint i = 1;
i++; // == 1 but i == 2

But ++i returns the actual incremented value:

uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

### Github Permalinks
+= 1
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L195

### Mitigation
Replace to ++i.


## increments can be unchecked in loops
### Summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:

    for (uint256 i; i < numIterations; i++) {
    // ...
    }

    to

    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L443
### Mitigation
Add unchecked ++i at the end of all the for loop where it's not expected to overflow and remove them from the for header


## <array>.length should no be looked up in every loop of a for-loop
### Summary
In loops not assigning the length to a variable so memory accessed a lot (caching local variables)

### Details
The overheads outlined below are PER LOOP, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)

- Length is not being cached / it is recovered using a function but still looked up in every loop

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L443

### Mitigation
Assign the length of the array.length to a local variable in loops for gas savings


## Variables should be cached when used several times
### Summary
Variables read more than once improves gas usage when cached into local variable
### Details
In loops or state variables, this is even more gas saving

### Github Permalinks
- id
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L125-L129
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L102-L106

- marketIndex
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L195-L234

- controller
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L187-L239

- tresuary
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L187-L239

- asset
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L227
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L189-L190

- cast of address(insrStake) and address(riskStake) can be stored in local variables
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L120-L121

- admin, govToken
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L100-L111

- vaultFactory.getVaults(marketIndex);
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L200-L206
### Mitigation
Cache variables used more than one into a local variable.

## Functions guaranteed to revert when called by normal users can be marked payable
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L186
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L253
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L295
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L310
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L329
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L347
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L366
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L85
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L74
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L130
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L53
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L215
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L225
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L216
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L117

### Mitigation
It's suggested to add payable to functions guaranteed to revert when called by normal users to improve gas costs

## >= cheaper than >
### Summary
Strict inequalities ( > ) are more expensive than non-strict ones ( >= ). This is due to some supplementary checks (ISZERO, 3 gas)

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L134

### Mitigation
Consider using >= 1 instead of > 0 to avoid some opcodes


## <X> += <Y> costs more gas than <X> = <X> + <Y> for state variables
### Summary
x+=y costs more gas than x=x+y for state variables

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L195

### Mitigation
Don't use += for state variables as it cost more gas.

## Unused named returns
### Summary
Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity 
### Details
Also as returns variable is ignored, it wastes extra gas

### Github Permalinks

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L264
        returns (int256 nowPrice)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L317
    function getVaultFactory() external view returns (address) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L162
        returns (uint256 shares)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L185
        returns (uint256 shares)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L213
        returns (uint256 shares)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L263
        returns (uint256 feeValue)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L381
        returns (uint256 entitledAmount)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L441
        returns (uint256 nextEpochEnd)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L186
    ) public onlyAdmin returns (address insr, address rsk) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L388
        returns (address[] memory vaults)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L49
        returns (

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L89
    function getOracle1_Price() public view returns (int256 price) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L112
    function getOracle2_Price() public view returns (int256 price) {

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L86
        returns (address insr, address risk)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L148
        returns (bytes32 hashedIndex)


### Mitigation
Remove return or returns when both used

## abi.encode() is less gas efficient than abi.encodePacked()
### Summary
In general, abi.encodePacked is more gas-efficient.

### Details
Changing the abi.encode function to abi.encodePacked can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, abi.encodePacked is more gas-efficient.
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L118
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L150
### Mitigation
Consider changing abi.encode to abi.encodePacked

## Retrieve internal variables directly
### Summary
Save ~30 gas per call to each function

### Example
epochsLength();
to
epochs.length;

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L443

### Mitigation
Retrieve internal variables directly rather than calling the retrieving function


## Unnecessary storage read
### Summary
A value which value is already known can be used directly rather than reading it from the storage
### Example
```
    modifier updateReward(address account) {
        rewardPerTokenStored = rewardPerToken();
        lastUpdateTime = lastTimeRewardApplicable();
        if (account != address(0)) {
            rewards[account] = earned(account);
            userRewardPerTokenPaid[account] = rewardPerTokenStored;
        }
        _;
    }
```

`rewardPerTokenStored` is previously calculated with `rewardPerToken();`, this can be stored in a local variable for not reading again the storage.

Recommendation
Change to:

```
    modifier updateReward(address account) {
        uint _rewardPerToken = rewardPerToken();
        rewardPerTokenStored = _rewardPerToken;
        lastUpdateTime = lastTimeRewardApplicable();
        if (account != address(0)) {
            rewards[account] = earned(account);
            userRewardPerTokenPaid[account] = _rewardPerToken;
        }
        _;
    }
```

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L65

- `_rewardsDuration` should be used rather than `rewardsDuration`
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L231
### Mitigation
Set directly the value to avoid unnecessary storage read to save some gas


## Make immutable state variables that do not change but assigned in the constructor
### Summary
State variables which value isn't changed by any function in the contract but constructor, can be declared as a immutable state variable to save some gas during deployment.

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L68-L70
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L137
### Mitigation
- Add immutable to state variables that do not change but which value is assigned in constructor

## Code follows CEI pattern and can actually remove nonReentrant modifier
### Summary
As it follows CEI pattern, even with a reentrancy, this reentrancy won't be harmful. Using nonReentrant in such scenario just consumes extra gas.
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L92
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L116
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L132
### Mitigation
Remove nonReentrant where the code follows CEI pattern.
