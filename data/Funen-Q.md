1. Incorrect Reason String

Files : 

1.) https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L103

Reason string can be used `Zero Timestamp !`

another case : 

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L126

2. Missing checked zero addresses

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L41

This can be checked if was no zero addresses by using if revert().

example : https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L126-L130

3. Missing File Directroy

This location file in directory has changed or it can't found, and u must update them and adding/import some contract to avoid running/error. This list down below for some file must be imported :

Files : 

1.) https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L4-L8

```
"@solmate/tokens/ERC20.sol"
"@chainlink/interfaces/AggregatorV3Interface.sol" -> which changed to "chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol"
"@chainlink/interfaces/AggregatorV2V3Interface.sol" ->  which changed to "@chainlink/contracts/src/v0.8/interfaces/AggregatorV2V3Interface.sol"
```

Same case : 

2.) https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

3.) https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol

4. Missing Indexed 

Each event should use three indexed fields if there are three or more fields

Files : 

1.) https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L35-L41

2.) https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L51-L58

3.) https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L69-L80

4.) https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L49-L56

5. Natspec Incomplete

Files :

1.) https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Controller.sol#L315

Don't forget to update the NatSpec


6. Typo

Files :

1.) https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L90

`previewDeposit` missing space

2.) https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L434

`epochs'` into `epochs`


7. Simplify Word

Files : 

1.) https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L54

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L18

just use `NotAdmin()`

2.) https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L41

`mIndex` into `marketIndex`



