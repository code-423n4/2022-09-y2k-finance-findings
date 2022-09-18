
### [G-01] BOOLEAN COMPARISONS

There is 5 instance of this issue:

> **File : rewards/RewardFactory** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L96
>
> **File : Controller.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L93
>
> **File : Controller.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L211
>
> **File : Vault.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L88
>
> **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L217
>
> **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L314

#### Mitigation
For example :

> if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd)
 > ** TO **
> if(Vault(_insrToken).idExists(_epochEnd) || Vault(_riskToken).idExists(_epochEnd)    
    
The results of Vault(_insrToken).idExists(_epochEnd) already in true or false, so need to compair those with booleans





### [G-02] NO NEED TO EXPLICITLY INITIALIZE VARIABLES WITH DEFAULT VALUES
Here Uints initiaziled with 0, which is not necessary cause its by default value is zero

There is 3 instance of this issue:

> **File : rewards/StakingRewards.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L36
>
> **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443
>
> **File : VoultFactory.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L159

#### Mitigation
No need to initialize with 0





### [G-03] EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING
The function is with empty block

There is 3 instance of this issue:

> **File : rewards/RewardsDistributionRecipient.sol**  => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsDistributionRecipient.sol#L18
>
>**File : SemiFungibleVault.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L277-L281
>
>**File : SemiFungibleVault.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L283-L287

#### Mitigation
It should do something.
At least emits some events.




### [G-4] ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()

There is 2 instance of this issue:

> **File : rewards/RewardFactory** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L118
>
> **File : rewards/RewardFactory** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L150

#### Mitigation
Use abi.encodePacked()




### [G-05] UNNECESSARY USE OF SAFEMATH

There is 1 instance of this issue:
> **File : rewards/StakingRewards.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L4

#### Mitigation
There is no need to use SafeMath above solidity 0.8.0 as it by default checks for over and underflow condition




### [G-06] >= COSTS LESS GAS THAN >

There is 2 instance of this issue:

> **File : Vault.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L88
>
> **File : Vault.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L96




### [G-07] FUNCTION FOR LENGTH SHOULD GET CACHED IN FOR LOOP 

here the return value for epochsLength() should cached first and then used in loop

> **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443




### [G-08] FUNCTION THAT COULD BE EXTERNAL

To save gas consumption function should declare external, which does not call inside that smartcontract by other functions

There is 3 instance of this issue: 

> **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L438
>
> **File : VoultFactory.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L295
>
> **File : VoultFactory.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L366



### [G-09] SHOULD OPTIMIZED FOR LOOP

There is 1 instance of this issue: 

> **File :  Voult.sol** => https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

. Should not initialize uint with default value i.e uint i=0 **TO** uint i;
. Should cached the length function to memory stack then used that memory variable for loop condition check
. Should use ++i instead i++
. Should uncheck i++