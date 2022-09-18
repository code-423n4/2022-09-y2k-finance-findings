### [L-01] Absence Of Zero Address Checks 

Zero address checks absent, before assigning function parameters to stateVariables

There is 10 instance of this issue:

> **File : rewards/RewardsDistributionRecipient.sol**  => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsDistributionRecipient.sol#L1
>
> **File : rewards/RewardFactory** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L68-L70
>
> **File : rewards/StakingRewards** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L60-L68
>
>**File : SemiFungibleVault.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L70
>
> **File : VoultFactory.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L180
>
> **File : VoultFactory.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L184
>
> **File : VoultFactory.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L312

#### Recommended Mitigation
Write code for Zero address check before assigning them to state variable . 




### [L-02] Missing Of License (SPDX-License-Identifier Missing)

License Identifier missing in some contract files

There is 2 instance of this issue:

> **File : rewards/Owned.sol**  => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/Owned.sol#L1
> **File : rewards/RewardsDistributionRecipient.sol**  => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsDistributionRecipient.sol#L1

#### Recommended Mitigation
Should mention License Identifier




### [L-03] Modifiers Changing State Variables
Its not recommended that modifiers able to change state variables,
So either all those functionality should be enclosed in a private function and further use those in other functions instead of modifiers .

There is 1 instance of this issue:

> **File : rewards/StakingRewards** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L60-L68




### [L-04] Return value of TransferFrom() not checked

There is 4 instance of this issue:

> **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L167  
>
> **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L228
>
> **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L231
>
> **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L365

#### Recommended Mitigation
Return value of transferFrom() should checked before further proceeding in code base.
Or consider using safeTransfer() / safeTransferFrom() instead of transfer() / transferFrom()




### [L-05] Use Require() / Revert() instead of Assert()
When a function call fails then in case of assert() all remaining gas is consumed
But in case of require() and revert() remaining gas were send back to Caller,

So its recommended to use require/revert instead of assert in perspective of gas optimization

There is 1 instance of this issue:
 > **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190


### [N-01] Misleading Comment
There is 1 instance of this issue:
 > **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L265

It saying // 0.5% = multiply by 1000 then divide by 5
where actually it will multiply by 5 divide by 1000 for 0.5%

