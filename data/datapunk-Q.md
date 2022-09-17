## Impact
constructor of pegOracle.sol does not check if the correct oracles are used.
Wrong oracle might be used without doing so.

## Proof of Concept
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L22

## Tools Used

## Recommended Mitigation Steps
emit oracle1/2.description() as event, which returns a description for this data feed. Usually this is an asset pair for a price feed.


## Impact
Detailed description of the impact of this finding.

## Proof of Concept
Provide direct links to all referenced code in GitHub. Add screenshots, logs, or any other relevant proof that illustrates the concept.

## Tools Used

## Recommended Mitigation Steps


## Impact
constructor of pegOracle checks if two priceFeed have the same decimal.
This can be restrictive. It allows more possibilities if adjustment to 18 deciamls is done separately on each priceFeed, then compare.

## Proof of Concept
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L29
## Tools Used

## Recommended Mitigation Steps
run decimal adjustment on both priceFeeds individually, then compare prices.


## Impact
10000 and 1000000 seem like magic numbers. In the worst case, they might be inteneded to be the same, thus have wrong nowPrice

## Proof of Concept
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L68
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L78

## Tools Used

## Recommended Mitigation Steps
check if these two numbers are intended to be the same, otherwise justify them in comments

## Impact
the intention of this code snippet is not documented nor reflected in the test cases. If as designed, make it clear in the comments
        if (price1 > price2) {
            nowPrice = (price2 * 10000) / price1;
        } else {
            nowPrice = (price1 * 10000) / price2;
        }
## Proof of Concept
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L67
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/test/AssertTest.t.sol#L79

## Tools Used

## Recommended Mitigation Steps
Confirm it is as designed



## Impact
Comment says "for example, hedgeY2K/riskY2K", yet it should be defined as hY2K/rY2K. Misname of the symbol will treat both sides as hedge

## Proof of Concept
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L109
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L388

## Tools Used

## Recommended Mitigation Steps
make sure symbol for risk is fixed in the comment.


## Impact
functions only accessed externally, should be set as external instead of public

## Proof of Concept
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L277
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L287
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L295
etc ...

## Tools Used

## Recommended Mitigation Steps
change public to external


## Impact
oracle and token in VaultFactory's createNewMarket should match up

## Proof of Concept
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L222

## Tools Used

## Recommended Mitigation Steps
oracle can return a description and token can return a name/symbol
at least emit these info to show correct oracle is used for the token



## Impact
why use SafeMath under 0.8.15?

## Proof of Concept
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L29

## Tools Used

## Recommended Mitigation Steps



## Impact
no epochHasNotStarted(id) modifier applied to depositETH. Although it will eventually checked by deposit. It is advisable to check early, so to save gas

## Proof of Concept
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L182

## Tools Used

## Recommended Mitigation Steps

    function depositETH(uint256 id, address receiver)
        external
        payable
        epochHasNotStarted(id)
        returns (uint256 shares)
    {
        ...
    }


## Impact
Given the specific Vault assigning totalSupply to totalAssets, and the convertion logic does mulDiv(totalSupply, totalAssets), to save gas, we can remove all the conversion logic and use assets directly as shares.

## Proof of Concept
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L251
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L152

## Tools Used

## Recommended Mitigation Steps

