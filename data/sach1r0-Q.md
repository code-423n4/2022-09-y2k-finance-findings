# Lack of zero-address check in the constructor

## Details
 Lack of zero-address checks may lead to infunctional protocol especially in the case wherein variable is immutable like the  `asset` .
 
## Mitigation
Consider adding zero-address checks such as: `require(_asset != address(0));`

## Line of code
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L65-L73
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/RewardsFactory.sol#L63-L71


___
# Lack of indexed parameters in events

## Details
Some of the events in the codebase are not indexed. Indexing event parameters enable off-chain services to search and filter for specific events.
see reference: Low severity finding from OpenZeppelin Audit of HoldeFi
[L09] Lack of indexed parameters in events
https://blog.openzeppelin.com/holdefi-audit/#low

## Mitigation
Add the `indexed` keyword to the events.

## Line of code
https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L49-L56
___
# Open TODO

## Mitigation
I suggest avoiding open TODOs as they may indicate errors that still needs to be fixed

## Line of code
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L196

___
# `deployMoreAssets()` is an admin-only function but does not emit an event

## Details
Admin-only functions that change critical parameters should emit events. Events allow capturing the changed parameters so that offchain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.   
                         
## Mitigation
I suggest emitting an event like the other admin-only function does.

## Line of Code
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L248-L266