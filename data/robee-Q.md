
## Duplicates in array


        You allow in some arrays to have duplicates. Sometimes you assumes there are no duplicates in the array.

### Code instances:

VaultFactory._createEpoch pushed (_marketVault)




## Loss Of Precision

This issue is about arithmetic computation that could have been done more percise.
The following are places in the codebase in which you multiplied after the divisions.
Doing the multiplications at start lead to more accurate calculations.
This is a list of places in the code that this appears (Solidity file, line number, actual line):

### Code instances:

        Vault.sol, 418, entitledAmount = amount.divWadDown(idFinalTVL[id]).mulDivDown( idClaimTVL[id], 1 ether );
        Vault.sol, 398, entitledAmount = amount.divWadDown(idFinalTVL[id]).mulDivDown( idClaimTVL[id], 1 ether ) + amount;
        Vault.sol, 406, entitledAmount = amount.divWadDown(idFinalTVL[id]).mulDivDown( idClaimTVL[id], 1 ether );



## Mult instead div in compares


To improve algorithm precision instead using division in comparison use multiplication in the following scenario:

        Instead a < b / c use a * c < b.

In all of the big and trusted contracts this rule is maintained.

### Code instance:

        StakingRewards.sol, 202, require( rewardRate <= balance.div(rewardsDuration), "Provided reward too high" );



## Missing fee parameter validation


Some fee parameters of functions are not checked for invalid values. Validate the parameters:


### Code instances:

        VaultFactory.deployMoreAssets (_withdrawalFee)
        Vault.createAssets (_withdrawalFee)
        VaultFactory.createNewMarket (_withdrawalFee)



## Require with not comprehensive message

The following requires has a non comprehensive messages.
This is very important to add a comprehensive message for any require. Such that the user has enough
information to know the reason of failure:

### Code instances:

        Solidity file: Vault.sol, In line 165 with Require message: ZeroValue
        Solidity file: Vault.sol, In line 187 with Require message: ZeroValue



## Not verified input


external / public functions parameters should be validated to make sure the address is not 0.
Otherwise if not given the right input it can mistakenly lead to loss of user funds.

### Code instances:

        Vault.sol.sendTokens _counterparty
        Vault.sol.withdraw receiver
        VaultFactory.sol.createNewMarket _token
        SemiFungibleVault.sol.deposit receiver
        Vault.sol.depositETH receiver
        VaultFactory.sol.createNewMarket _oracle
        RewardsDistributionRecipient.sol.setRewardsDistribution _rewardsDistribution
        Vault.sol.deposit receiver
        SemiFungibleVault.sol.withdraw receiver
        StakingRewards.sol.recoverERC20 tokenAddress



## Treasury may be address(0)


    Make sure the treasury is not address(0).


### Code instance:

        VaultFactory.sol.changeTreasury _treasury



## Not verified owner


        owner param should be validated to make sure the owner address is not address(0).
        Otherwise if not given the right input all only owner accessible functions will be unaccessible.


### Code instances:

        SemiFungibleVault.sol.withdraw owner
        Owned.sol.nominateNewOwner _owner
        Vault.sol.withdraw owner



## Named return issue

Users can mistakenly think that the return value is the named return, but it is actually the actualreturn statement that comes after. To know that the user needs to read the code and is confusing.
Furthermore, removing either the actual return or the named return will save gas.

### Code instances:

        PegOracle.sol, latestRoundData
        PegOracle.sol, getOracle2_Price
        PegOracle.sol, getOracle1_Price
        Vault.sol, deposit
        Vault.sol, depositETH
        Vault.sol, withdraw
        VaultFactory.sol, createNewMarket
        Controller.sol, getLatestPrice
        VaultFactory.sol, getVaults
        Vault.sol, beforeWithdraw
        RewardsFactory.sol, getHashedIndex
        Vault.sol, calculateWithdrawalFeeValue
        Vault.sol, getNextEpoch
        RewardsFactory.sol, createStakingRewards



## Missing non reentrancy modifier

The following functions are missing reentrancy modifier although some other pulbic/external functions does use reentrancy modifer.
Even though I did not find a way to exploit it, it seems like those functions should have the nonReentrant modifier as the other functions have it as well..

### Code instances:

        Vault.sol, sendTokens is missing a reentrancy modifier
        Vault.sol, withdraw is missing a reentrancy modifier
        StakingRewards.sol, recoverERC20 is missing a reentrancy modifier
        Vault.sol, depositETH is missing a reentrancy modifier



## Assert instead require to validate user inputs


        From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix.
        With assert the user pays the gas and with require it doesn't. The ETH network gas isn't cheap and users can see it as a scam.

### Code instance:

        Vault.sol : reachable assert in line 189



## Never used parameters

Those are functions and parameters pairs that the function doesn't use the parameter. In case those functions are external/public this is even worst since the user is required to put value that never used and can misslead him and waste its time.

### Code instances:

        SemiFungibleVault.sol: function beforeWithdraw parameter shares isn't used. (beforeWithdraw is internal)
        SemiFungibleVault.sol: function beforeWithdraw parameter id isn't used. (beforeWithdraw is internal)
        SemiFungibleVault.sol: function afterDeposit parameter shares isn't used. (afterDeposit is internal)
        SemiFungibleVault.sol: function afterDeposit parameter assets isn't used. (afterDeposit is internal)
        SemiFungibleVault.sol: function afterDeposit parameter id isn't used. (afterDeposit is internal)
        SemiFungibleVault.sol: function beforeWithdraw parameter assets isn't used. (beforeWithdraw is internal)



## Open TODOs

Open TODOs can hint at programming or architectural errors that still need to be fixed.
These files has open TODOs:

### Code instance:

Open TODO in Vault.sol line 195 :     @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards




## Check transfer receiver is not 0 to avoid burned money


Transferring tokens to the zero address is usually prohibited to accidentally avoid "burning" tokens by sending them to an unrecoverable zero address.


### Code instances:

        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L221
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L190
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L93
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L99
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L167
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L136
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L365
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L228
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L122
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L127



## Missing commenting


        The following functions are missing commenting as describe below:

### Code instance:

        Owned.sol, nominateNewOwner (external), parameter _owner not commented



## Add a timelock

To give more trust to users: functions that set key/critical variables should be put behind a timelock.

### Code instances:

        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L295
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L350
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L225
        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsDistributionRecipient.sol#L20




## Div by 0


Division by 0 can lead to accidentally revert,
(An example of a similar issue - https://github.com/code-423n4/2021-10-defiprotocol-findings/issues/84)

### Code instance:

        https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L70 price2 might be 0



## transfer return value of a general ERC20 is ignored

Need to use safeTransfer instead of transfer. As there are popular tokens, such as USDT that transfer/trasnferFrom method doesn’t return anything. The transfer return value has to be checked (as there are some other tokens that returns false instead revert), that means you must
 1. Check the transfer return value
Another popular possibility is to add a whiteList.
Those are the appearances (solidity file, line number, actual line):

### Code instances:

        Vault.sol, 365 (sendTokens), asset.transfer(_counterparty, idFinalTVL[id]);
        Vault.sol, 167 (deposit), asset.transferFrom(msg.sender, address(this), shares);
        Vault.sol, 231 (withdraw), asset.transfer(receiver, entitledShares);
        Vault.sol, 190 (depositETH), assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
        Vault.sol, 228 (withdraw), asset.transfer(treasury, feeValue);

