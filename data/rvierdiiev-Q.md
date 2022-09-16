1.	Remove variable as it is nod used.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L11
2.	Modifier is not used.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L73-L77
3.	Conditions with assertions to true or false are confusing. 
Use `!vault.idExists(epochEnd)` instead.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L93
Use `if(!insrVault.idExists(epochEnd) || !riskVault.idExists(epochEnd))` instead
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L211
Use `!idExists[id]` instead
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L80
Use `!isApprovedForAll(owner, receiver)` instead
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L217
Use `if(idExists[epochEnd])` instead
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L314
4.	Check address param for 0 address.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L70
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L96
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L80
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L365
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L312
5.	Add event description.
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L113
