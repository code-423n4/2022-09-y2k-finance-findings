## [NAZ-L1] Value Range Validity for Setters
**Severity** Low
**Context**: [`VaultFactory.sol#L327`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L327)

**Description**:
These functions doesn't have any checks to ensure that the variables being set is within some kind of value range.

**Recommendation**:
Each variable input parameter updated should have it's own value range checks to ensure their validity.


## [NAZ-L2] Missing Equivalence Checks in Setters
**Severity**: Low
**Context**: [`Vault.sol#L277`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L277), [`Vault.sol#L287`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L287), [`Vault.sol#L295`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L295), [`VaultFactory.sol#L295`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L295), [`VaultFactory.sol#L308`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L308), [`VaultFactory.sol#L327`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L327), [`VaultFactory.sol#L345`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L345), [`VaultFactory.sol#L366`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L366)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-L3] Missing Time locks
**Severity**: Low 
**Context**: [`VaultFactory.sol#L295`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L295), [`VaultFactory.sol#L308`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L308), [`VaultFactory.sol#L327`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L327), [`VaultFactory.sol#L345`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L345), [`VaultFactory.sol#L366`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L366)

**Description**:
When critical parameters of systems need to be changed, it is required to broadcast the change via event emission and recommended to enforce the changes after a time-delay. This is to allow system users to be aware of such critical changes and give them an opportunity to exit or adjust their engagement with the system accordingly. None of the onlyOwner functions that change critical protocol addresses/parameters have a time-lock for a time-delayed change to alert: (1) users and give them a chance to engage/exit protocol if they are not agreeable to the changes (2) team in case of compromised owner(s) and give them a chance to perform incident response.

**Recommendation**:
Users may be surprised when critical parameters are changed. Furthermore, it can erode users' trust since they can’t be sure the protocol rules won’t be changed later on. Compromised owner keys may be used to change protocol addresses/parameters to benefit attackers. Without a time-delay, authorized owners have no time for any planned incident response.


## [NAZ-L4] Missing Zero-address Validation
**Severity**: Low
**Context**: [`SemiFungibleVault.sol#L66`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#l66), [`VaultFactory.sol#L308`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L308), [`StakingRewards.sol#L74-L76`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L74-L76), [`RewardsFactory.sol#L64-L66`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L64-L66)

**Description**:
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

**Recommendation**:
Consider adding explicit zero-address validation on input parameters of address type.


## [NAZ-N1] Function States Named Return But Doesn't Use It
**Severity** Informational
**Context**: [`PegOracle.sol#L89`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L89), [`PegOracle.sol#L128`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L128)

**Description**:
The linked function(s) have a named return that is not used. Using both named returns and a return statement isn't necessary. 

**Recommendation**:
Removing one of those can improve code clarity.


## [NAZ-N2] Line Length
**Severity**: Informational
**Context**: [`Vault.sol#L109`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L109), [`Vault.sol#L147-L149`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L147-L149), [`Vault.sol#L178-L179`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L178-L179), [`Vault.sol#L197-L199`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L197-L199), [`Vault.sol#L242`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L242), [`Vault.sol#L303-L305`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L303-L305), [`Vault.sol#L332-L333`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L332-L333), [`Vault.sol#L346-L347`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L346-L347), [`Vault.sol#L356-L358`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L356-L358), [`Vault.sol#L374`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L374), [`VaultFactory.sol#L168-L169`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L168-L169), [`VaultFactory.sol#L172-L173`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L172-L173), [`VaultFactory.sol#L242`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L242), [`VaultFactory.sol#L244-L246`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L244-L246), [`VaultFactory.sol#L263`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L263), [`RewardsFactory.sol#L25`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L25), [`RewardsFactory.sol#L27`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L27)

**Description**:
Max line length must be no more than 120 but many lines are extended past this length.

**Recommendation**:
Consider cutting down the line length below 120.

## [NAZ-N3] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`SemiFungibleVault.sol#L110`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L110), [`VaultFactory.sol#L19`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L19), [`StakingRewards.sol#L143`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L143)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Functions should then further be ordered with view functions coming after the non-view labeled ones.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N4] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`Controller.sol#L13-L15`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L13-L15), [`PegOracle.sol#L15-L16`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L15-L16), [`PegOracle.sol#L89`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L89), [`PegOracle.sol#L112`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L112), [`PegOracle.sol#L22`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L22), [`SemiFungibleVault.sol#L276`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L276), [`SemiFungibleVault.sol#L282`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L282), [`Vault.sol#L35`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L35), [`Vault.sol#L37`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L37), [`VaultFactory.sol#L85`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L85), [`VaultFactory.sol#L91`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L91), [`VaultFactory.sol#L97`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L97), [`VaultFactory.sol#L103`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L103), [`VaultFactory.sol#L109`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L109), [`VaultFactory.sol#L113`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L113)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Private variables and functions should lead with an `_underscore`. Events should be in `CamelCase`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the [Solidity style guide](https://docs.soliditylang.org/en/latest/style-guide.html). 


## [NAZ-N5] Unindexed Event Parameters
**Severity** Informational
**Context**: [`Controller.sol#L49`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L49), [`StakingRewards .sol#L56`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L56)

**Description**:
Parameters of certain events are expected to be indexed so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events.

**Recommendation**:
Consider adding the indexed keyword to event parameters that should include it.


## [NAZ-N6] Commented Out Code
**Severity**: Informational
**Context**: [`Controller.sol#L25`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L25), [`Controller.sol#L268-L270`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L268-L270), [`Vault.sol#L393-L398`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L393-L398)

**Description**:
There is commented code that makes the code messy and unneeded. 

**Recommendation**:
Consider removing the commented out code.


## [NAZ-N7] TODOs Left In The Code
**Severity**: Informational
**Context**: [`Vault.sol#L196`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L196)

**Description**:
There should never be any TODOs in the code when deploying.

**Recommendation**:
Consider finishing the TODOs before deploying.


## [NAZ-N8] Use Underscores for Number Literals
**Severity**: Informational
**Context**: [`Controller.sol#L15`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L15), [`PegOracle.sol#L68`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L68), [`PegOracle.sol#L70`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L70), [`PegOracle.sol#L78`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L78), [`Vault.sol#L266`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L266)

**Description**:
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

**Recommendation**:
Consider using underscores for number literals to improve its readability.


## [NAZ-N9] Spelling Errors
**Severity**: Informational
**Context**: [`Vault.sol#L199 (transfered => transferred)`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L199), [`Vault.sol#L415 (transfered => transferred)`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L415), [`RewardsFactory.sol#L25 (insrance => insurance)`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L25), [`RewardsFactory.sol#L27 (insrance => insurance)`](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L27)

**Description**:
Spelling errors in comments can cause confusion to both users and developers.

**Recommendation**:
Consider checking all misspellings to ensure they are corrected.