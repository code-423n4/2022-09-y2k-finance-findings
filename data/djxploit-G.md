## Use >= or <= instead of > or < to save gas
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L88
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L311
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L96
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L257

## Use !=0 instead of > 0 inside require statements to save gas
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187

## Don't use multiple condition checks in a single if statements , instead use separate if statements to save gas
In line https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L96, instead of 
`if((block.timestamp < id) && idDepegged[id] == false)` use 
```
if(block.timestamp < id) {
     if(idDepegged[id] == false) {
        revert EpochNotFinished();
```
Similarly in lines : 
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L215-L217

## Optimise `for` loops to save gas
In line https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443-L450, optimise the for loop as shown below
```
uint256 len = epochsLength(); // cache the length
for (uint256 i; i < len;) { // avoid initialisation of i
            if (epochs[i] == _epoch) {
                if (i == epochsLength() - 1) {
                    return 0;
                }
                return epochs[i + 1];
            }
            unchecked {
            ++i; // use unchecked and ++i
            }
        }
```

## Don't emit storage variables
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L225 : `marketIndex` is storage variable

## Avoid unnecessary initialisation to save gas
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L159 : 0 initialisation not required

## Optimise if statements to save gas
In line https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L314, the if statement can be changed to :
`if(idExists[epochEnd])`

## Cache variables to save gas
In lines, https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L278-L287, `_marketVault.index`, `_marketVault.epochBegin`, `_marketVault.epochEnd`, `_marketVault.epochEnd` and `_marketVault.withdrawalFee` are used for 2nd time. They should be cached before using , in order to save MLOAD gas

## Changing function visibility from public to external saves gas
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L178, function `createNewMarket` can be marked external
Similarly `deployMoreAssets` function : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L248
`setController` : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L295
`changeOracle`: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L366
`getVaults`: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L385
`changeTreasury`: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L277
`changeTimewindow`: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L287
`changeController`: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L295
`createAssets`: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L295
`endEpoch`: https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L336


## Multiple address mappings can be combined into a single mapping of an address to a struct
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. Such optimisation is applicable in :
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L119-L120
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L47-L55

