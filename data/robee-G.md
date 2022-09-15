## Unnecessary equals boolean


Boolean variables can be checked within conditionals directly without the use of equality operators to true/false.

### Code instances:

        Controller.sol, 211: if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
        Vault.sol, 314: if(idExists[epochEnd] == true)
        Vault.sol, 217: isApprovedForAll(owner, receiver) == false)
        RewardsFactory.sol, 96: if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)



## State variables that could be set immutable

In the following files there are state variables that could be set immutable to save gas. 

### Code instances:

        oracle2 in PegOracle.sol
        decimals in PegOracle.sol
        oracle1 in PegOracle.sol
        priceFeed1 in PegOracle.sol
        priceFeed2 in PegOracle.sol



## Prefix increments are cheaper than postfix increments

Prefix increments are cheaper than postfix increments. 
Further more, using unchecked {++x} is even more gas efficient, and the gas saving accumulates every iteration and can make a real change
There is no risk of overflow caused by increamenting the iteration index in for loops (the `++i` in `for (uint256 i = 0; i < numIterations; ++i)`).
But increments perform overflow checks that are not necessary in this case.
These functions use not using prefix increments (`++x`) or not using the unchecked keyword: 

### Code instance:

        change to prefix increment and unchecked: Vault.sol, i, 443



## Unnecessary index init


In for loops you initialize the index to start from 0, but it already initialized to 0 in default and this assignment cost gas. 
It is more clear and gas efficient to declare without assigning 0 and will have the same meaning:

### Code instance:

        Vault.sol, 443



## Unnecessary default assignment


Unnecessary default assignments, you can just declare and it will save gas and have the same meaning.
    

### Code instance:

        StakingRewards.sol (L#36) : uint256 public periodFinish = 0;



## Short the following require messages

The following require messages are of length more than 32 and we think are short enough to short
them into exactly 32 characters such that it will be placed in one slot of memory and the require 
function will cost less gas. 
The list: 

### Code instances:

        Solidity file: StakingRewards.sol, In line 217, Require message length to shorten: 33, The message: Cannot withdraw the staking token
        Solidity file: PegOracle.sol, In line 24, Require message length to shorten: 34, The message: oracle2 cannot be the zero address
        Solidity file: PegOracle.sol, In line 23, Require message length to shorten: 34, The message: oracle1 cannot be the zero address



## Use != 0 instead of > 0


Using != 0 is slightly cheaper than > 0. (see https://github.com/code-423n4/2021-12-maple-findings/issues/75 for similar issue)


### Code instance:

        StakingRewards.sol, 119: change 'amount > 0' to 'amount != 0'



## Inline one time use functions


The following functions are used exactly once. Therefore you can inline them and save gas and improve code clearness.
    

### Code instances:

        SemiFungibleVault.sol, beforeWithdraw
        SemiFungibleVault.sol, afterDeposit
        Owned.sol, _onlyOwner



## Do not cache msg.sender


We recommend not to cache msg.sender since calling it is 2 gas while reading a variable is more.


### Code instance:

        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L134

