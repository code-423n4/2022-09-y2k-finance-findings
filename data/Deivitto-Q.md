# QA
# Low
## Prevent div by 0
### Impact
Divisions by 0 will revert the code. In `PegOracle.sol`, price1 and price 2 can be 0 if for example oracle fails.

That will cause to revert on `latestRoundData`. Oracle `price1` and `price2` variable should be checked previously to the division and emit some event if something goes wrong

### Proof of Concept
Navigate to the following contracts,

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L68
            nowPrice = (price2 * 10000) / price1;//@audit revert div if 0

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L70
            nowPrice = (price1 * 10000) / price2;//@audit revert div if 0


If oracle fails, variable will be equal to zero therefore div by zero will occur.

### Recommended Mitigation Steps
Recommend making sure division by 0 won’t occur by checking the variables beforehand and handling this edge case.


## Missing checks for address(0x0) when assigning values to `address` state or `immutable` variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems.
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L70-L72
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L68-L70
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L81-L83
### Mitigation
Check zero address before assigning or using it


## Missing checks for address(0x0) on `changeTreasury()`
### Summary
Zero address should be checked for some function parameters.

### Details
`treasury` should be checked that is not the zero address while assigned, even if this is later checked in Vault.sol, code can use unnecesary gas for assigning tresuary value to 0 and then realize of the error and change it again.

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L312

### Mitigation
Check zero address before assigning it


https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L261

## block.timestamp used as time proxy 
### Summary
Risk of using block.timestamp for time should be considered. 
### Details
block.timestamp is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L198-L248
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L261-L312
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L155-L157
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L183-L210
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L225-L232

### Mitigation
Consider using an oracle for precision
Consider the risk of using block.timestamp as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 


## Use of asserts()
### Impact

From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix.
With assert the user pays the gas and with require it doesn't. The ETH network gas isn't cheap and users can see it as a scam.
You have reachable asserts in the following locations (which should be replaced by require / are mistakenly left from development phase):

### Details
The Solidity assert() function is meant to assert invariants. Properly functioning code should never reach a failing assert statement. A reachable assertion can mean one of two things:

A bug exists in the contract that allows it to enter an invalid state;
The assert statement is used incorrectly, e.g. to validate inputs.

### References
SWC ID: 110

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L190
        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));


### Recommended Mitigation Steps
Substitute asserts with require/revert.

## Missing event for core paths/functions/parameters
### Summary
Events are important for critical parameter change.
### Details
Next functions should emit events for the core addresses changes
- `Vault.changeController(address)`
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L298
### Recommended Mitigation Steps
Public external functions where money / tokens are transferred / should emit some type of event for better monitoring

## Function in admin settings missing onlyAdmin modifier
### Impact
Methods expected to be only called by admins should indeed include the modifier as this can lead to critical damages.

Actually this methods is not expected to be called outside VaultFactory functions that include by now the modifier, however, if changed the code or called from another contract, this can lead to unexpected behaviors.

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L268-L289

### Mitigation
- Add the modifier or remove it from the onlyAdmin zone.
- Also in case of adding onlyAdmin, consider adding payable for less gas usage as mentioned in another of my findings: `Functions guaranteed to revert when called by normal users can be marked payable`


# Informational
## Comparison with a a boolean
### Summary
There are a number of instances where a boolean variable/function is checked.
### Details
- This check can be further simplified from `variable == false` to `!variable`.
- This check can be further simplified from `variable == true` to `variable`.

### Github Permalink

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L314
        if(idExists[epochEnd] == true)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L93
        if(vault.idExists(epochEnd) == false)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L211
        if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L96
        if((block.timestamp < id) && idDepegged[id] == false)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L217
            isApprovedForAll(owner, receiver) == false)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L96
        if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
### Mitigation
Simplify boolean comparisons in order to improve readability and save gas

##  Use of magic numbers is confusing and risky
### Summary
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
### Details
Values are hardcoded and would be more readable and maintainable if declared as a constant

1000000, 10000, 1000 are hardcoded, are so similar and can potentially include a typo.

1e18, 150, 1 ether are hardcoded and would be more legible if saved in constant

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L311
        if(_withdrawalFee > 150)
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L78
            nowPrice / 1000000,
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L68
            nowPrice = (price2 * 10000) / price1;

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L70
            nowPrice = (price1 * 10000) / price2;

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L266
        return (amount * epochFee[_epoch]) / 1000;

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L168
                    .mul(1e18)

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L177
                .div(1e18)
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L400-L421
1 ether
### Mitigation
Replace magic hardcoded numbers with declared constants.
Define constants for the numbers used throughout the code.
Comment what these numbers are intended for

## Missing indexed event parameters
### Summary
Events without `indexed` event parameters make it harder and
inefficient for off-chain tools to analyze them.
### Details
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L49-L56
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L51
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L55-L56

- Has so many parameters do only have 1 indexed
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L49-L56

- Has so many parameters do only have 2 indexed
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L69-L80

### Mitigation
Consider which event parameters could be particularly useful to off-chain tools and should be `indexed`.


## Missing Natspec 
### Summary 
Missing Natspec and regular comments affect readability and maintainability of a codebase. 
### Details 
Contracts has partial or full lack of comments
### Github Permalinks 
#### Natspec @param
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L76-L99
#### Natspec @param and @return value
- NOTE: All file
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L234-L270
#### Natspec @return value
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L428-L432
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L314-L320
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L134-L228

#### Documentation in general
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L268-L289
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L276-L286


 ### mitigation
 - Add @param descriptors
 - Add @return descriptors
 - Add Natspec comments. 
 - Add inline comments. 
 - Add comments for what the contract does

## Implementation of the comment spec confusing
### Summary
There are 2 instances of misleading comments.
- preventing overflow
```
        // Ensure the provided reward amount is not more than the balance in the contract.
        // This keeps the reward rate in the right range, preventing overflows due to
        // very high values of rewardRate in the earned and rewardsPerToken functions;
        // Reward + leftover must be less than 2^256 / 10^18 to avoid overflow.
```
However, since pragma 0.8.0 overflow is by default controlled. Also there is not a unchecked operation there what would explain why the comment about overflow is there. Also, operations involved as SafeMath is there for uint values will revert if overflow too

- misleading comment and incorrect implementation or comment spec
`// 0.5% = multiply by 1000 then divide by 5`
However, this assertion is not true, resulting value is 200. Also the code is not implementing what the comment says but what I think is really expected
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L265
        // 0.5% = multiply by 1000 then divide by 5//@audit misleading comment
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L197-L200
        // Ensure the provided reward amount is not more than the balance in the contract.
        // This keeps the reward rate in the right range, preventing overflows due to//@audit overflows
        // very high values of rewardRate in the earned and rewardsPerToken functions;
        // Reward + leftover must be less than 2^256 / 10^18 to avoid overflow.


### Mitigation
- Consider removing confusing comments and or implement what is intended.
- Consider adding an unchecked scenario for gas optimization if non overflow is expected, in that case the comment would make sense.

## Missing inheritance
###  Summary:
Contract is missing to inherit its dedicated interface
### Details
Controller is not inheriting from IController
### Github Permalink
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L10-L320
### Mitigation
Add the inheritance to the contract

## Commented out code
### Summary
There is some code that is commented out. It could point to items that are not done or need redesigning, be a mistake, or just be testing overhead.

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L25
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L393-L398
### Mitigation
Review and remove or resolve/document the commented out lines if needed.

## Open TODOs   
### Summary
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
### Details
The code includes a TODO already done that affects readability and focus on the readers/auditors of the contracts
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L196
    @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards
### Mitigation
Remove already done TODO

## Inconsistent spacing in comments
Some lines use // x and some use //x. The instances below point out the usages that don't follow the recommended `// x` style, within each file
### Details

`//Taking fee from the amount`
But following the style of the other comments would be:
`// Taking fee from the amount`
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L225
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L406
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L119-L121
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L197
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L25
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L27


## Duplicated ERC1155 constructor
### Summary
In `SemiFungibleVault.sol`, contract is ERC1155Supply, what is ERC1155.
However, `SemiFungibleVault.sol` also uses a explicit ERC1155 constructor, so at the end that constructor is being called twice.

### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L69

### Mitigation
  Remove is IERC721.

## So similar name in different variables or functions
### Summary
Names being so similar may lead to unexpected calls and assumptions.
### Github Permalinks
- variables
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L10-L11

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L15-L16

- functions
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L89
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L112
### Mitigation
Consider making more unique the names of this variables/functions


## Wrong or unexpected message 
### Summary
The message of the require occurs when timestamp == 0, however, a `!` is included at the end of the message, this can lead to wrong assumptions as:
- `!` is used for making a bool the opposite, this would mean the message to be: this happens when timestamp is not 0.
- If `!` is used for what grammatically is used for in english, there shouldn't be a whitespace between condition and exclamation and for clarity, timestamp == 0 should be separate from it with some kind of symbol as (). If the point is to get attention when monitoring, consider just adding some text in caps like `ALERT` or `WARNING`.
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L126
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L103
        require(timeStamp1 != 0, "Timestamp == 0 !");
### Mitigation
Change `Timestamp == 0 !` to `ALERT:Timestamp == 0` or similar changes

## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 79 (or 99) character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
### Github Permalinks 
- `+99`
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L96

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L121

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L152

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L197

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/SemiFungibleVault.sol#L213

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L85

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L93

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L108

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L109

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L112

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L147

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L148

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L149

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L178

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L179

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L192

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L196

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L197

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L198

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L199

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L220

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L242

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L303

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L304

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L305

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L332

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L333

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L334

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L346

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L347

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L348

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L356

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L357

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L358

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L374

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L384

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L385

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L121

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L168

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L169

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L171

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L172

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L173

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L174

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L234

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L242

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L244

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L245

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L246

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L263

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L272

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L273

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L278

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L381

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/VaultFactory.sol#L383

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L25

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L27

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L83

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/RewardsFactory.sol#L96

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L198

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L212

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L221

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L228
### Mitigation
Reduce line length to less than 99 at least to improve maintainability and readability of the code 

## Large multiples of ten should use scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000), for readability
### Summary
Multiples of 10 can be declared as constants with scientific notation so it's easier to read them and less prone to miss/exceed a 0 of the expected value.

### Details
Values 1000000, 10000, 1000 can be used in scientific notation
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L78
            nowPrice / 1000000,
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L68
            nowPrice = (price2 * 10000) / price1;

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/oracles/PegOracle.sol#L70
            nowPrice = (price1 * 10000) / price2;

https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Vault.sol#L266
        return (amount * epochFee[_epoch]) / 1000;

### Mitigation
Replace hardcoded numbers with constants that represent the scientific corresponding notation

## Operation can be better auto explained/coded
### Summary
In this case we find that a bool values are saved into a variable to only being used in next condition. 

This is actually developed as:

```
// Answer == 0: Sequencer is up
// Answer == 1: Sequencer is down
bool isSequencerUp = answer == 0;
if (!isSequencerUp) {
    revert SequencerDown();
}
```

and can be converted to:

```
if (answer != 0) {
    revert SequencerDown();
}
```
### Mitigation
Consider reducing the statement

## No need of local storing
### Impact
Saving values to be used just once can be confusing as when a variable is defined, readers expect to found that variable at least twice. However, this variables are declared but they can be inlined.
```
        } else {
            uint256 remaining = periodFinish.sub(block.timestamp);
            uint256 leftover = remaining.mul(rewardRate);
            rewardRate = reward.add(leftover).div(rewardsDuration);
        }
```

and can be

```
        } else {
            rewardRate = reward.add(
              (periodFinish.sub(block.timestamp)).mul(rewardRate)
              ).div(rewardsDuration);
        }
```
### Github Permalinks
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L192-L194

### Mitigation
Consider removing unnecessary variables

## Unused modifier
### Impact
Modifier `Controller.onlyAdmin` is defined but not used. This affects admin variable that is only used here. 
### Github Permalinks
- modifier
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L73-L77

- Expected admin settings
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/Controller.sol#L250-L252
### Mitigation
Remove or use it
