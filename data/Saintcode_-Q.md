## CONSIDER ADDING NONREENTRANT MODIFIERS IN SEMIFUNGIBLEVAULT `DEPOSIT`
Since SemiFungibleVault.sol is intended to be an EIP, adding further anti-reentrancy security checks is a must, so that users who inherit the contract and don't override `deposit` can get all the security checks

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L85

## BAD COMMENT EXPLAINING CALCULATED FEE
What ` return (amount * epochFee[_epoch]) / 1000;` does is multiply by fee then divide by 1000 
What says in the example comment in Vault.sol line 256:  // 0.5% = multiply by 1000 then divide by 5 is wrong, the correct sentence would be // 0.5% = multiply by 5then divide by 1000

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L265 
