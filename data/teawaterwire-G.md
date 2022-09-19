in [`SemiFungibleVault.sol`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol) : 

- since `deposit` and `withdraw` are overridden in `Vault.sol`, no need to implement them

- since `totalSupply(id)` and `totalAssets(id)` are the same thing all these methods can be removed: `convertToShares`, `convertToAssets`, `previewDeposit`, `previewMint`, `previewWithdraw`, `previewRedeem`

in [`Vault.sol`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol): 

- given the previous changes, in `deposit` this line can be removed just setting `shares = assets` : `require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");` 

- same in  `withdraw` : `shares = previewWithdraw(id, assets);` can be remove and using `shares  = assets`