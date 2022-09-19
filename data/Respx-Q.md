# Overview
The code base would benefit greatly from a clear, simple overview of the protocol. After reading the contest description many times, it took me many hours to realise two key points:
- Y2K doesn't actually deal with the tokens that are being hedged/insured. Instead, users deposit WETH and they are making bets with WETH that the assets will or won't depeg.
- The amounts in the hedge/risk vaults do not have to match, so one could be 10x the other, meaning the rewards for each type of depeg will vary according to market confidence.

Perhaps other wardens found these points self evident, but they were the insights I did not notice in the description which caused the ideas to "click" for me.

Contract files should include file header comments that give an overview of the contract, similar to the descriptions currently on the competition page.

Most functions do have comment headers, many of them helpful.

The code would also be clearer if significantly more comments were added within functions. Almost every line of code can usually be clarified, and this makes reading the code a great deal easier.

The first point in the *Non Critical* section is also particularly worth noting as it runs throughout the code.
___

# Non Critical
*(code style, clarity, syntax, versioning, off-chain monitoring (events, etc)*

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L162
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L336
The use of `epochEnd` as an ID number which exists alongside the marketplace ID number is needlessly confusing. Frequently, `epochEnd` is submitted to a function as a parameter and the parameter is called `id` (I have linked one example above, but there are more). This is very difficult to read. I strongly recommend renaming `epochEnd` as something like `epochEndId` or `epochId` and consistently using that for this value. It would also help reduce ambiguity to consistently name the marketplace ID variables something like `marketId`.

### VaultFactory.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L242
Further to the point above, I think this comment implies that the parameter `index` is actually the value of `epochEnd`, whereas is should be the market index.

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L268
`VaultFactory._createEpoch()` is missing its comment header

### Controller.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L317
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L12
Consider renaming this function `getVaultFactory()` to `getVaultFactoryAddress()` simply because its purpose seems to be to return an address, not a `VaultFactory` like the public getter on line 12.

### PegOracle.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L68-L70
Consider writing `10000` as `10_000` for clarity.
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L78
Similarly, consider writing `1000000` as `1_000_000` for clarity.

### Vault.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L150
The comment on line 150 should be punctuated, "from its shares", similar to line 180 (ie. remove the apostrophe on 150).

# Low 
*(e.g. assets are not at risk: state handling, function incorrect as to spec, issues with comments)*

### VaultFactory.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L312
`VaultFactory.changeTreasury()` modifies both the state variable `VaultFactory.treasury` but also the treasury value of the specified market index. This has gas implications, submitted separately, but could also cause an administration error. A change to a specific market ID might not be expected to change `VaultFactory`'s `treasury` state variable. Or a change that updates the state variable might be assumed to update all vaults.
Consider separating the function into two: one to change the state variable, the other to change the treasury within specified market vaults. This is the structure used in `setController()/changeController()`. 

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L184
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L221
In `createNewMarket`, because of the test on line 221, if `tokenToOracle[_token]` is already set, the value of `_oracle` will be silently discarded. This could have security implications if the previous oracle is no longer considered reliable. If a new oracle address is provided, it should be updated. Consider changing line 221 to:
`if (tokenToOracle[_token] != _oracle) {`
There should also be a `require` that `_oracle` is not the zero address.

### Controller.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L102
This test on line 102 should probably be `vault.idEpochBegin(epochEnd) >= block.timestamp)` as users will probably expect a begin time to be inclusive.

### Vault.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L110
The comment on line 110 is incorrect: `_token` is the token address, not the oracle address.

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L265
The comment on line 265 is incorrect. It reads:
*// 0.5% = multiply by 1000 then divide by 5*
It should be:
*// 0.5% = multiply by 5 then divide by 1000*

### PegOracle.sol and Controller.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L73
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L299
Both these contracts have expressions of the form `10**(18-(priceFeed.decimals()));`. Consider the case where the value of `priceFeed.decimals()` is greater than 18. This will cause both of these lines to revert, rendering the protocol incompatible with any such price feeds.