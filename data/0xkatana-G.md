# [G-01] Use != 0 instead of > 0

Using `> 0` uses slightly more gas than using `!= 0`. Use `!= 0` when comparing uint variables to zero, which cannot hold values below zero

Locations where this was found include
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L119
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L134
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L187
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L98
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L121

## Recommended Mitigation Steps

Replace `> 0` with `!= 0` to save gas

# [G-02] Short require strings save gas

Strings in solidity are handled in 32 byte chunks. A require string longer than 32 bytes uses more gas. Shortening these strings will save gas.

Locations where this was found include
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L118

## Recommended Mitigation Steps

Shorten all require strings to less than 32 characters

# [G-03] Use prefix not postfix in loops

Using a prefix increment (++i) instead of a postfix increment (i++) saves gas for each loop cycle and so can have a big gas impact when the loop executes on a large number of elements.

Locations where this was found include
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L443

## Recommended Mitigation Steps

Use prefix not postfix to increment in a loop

# [G-04] For loop incrementing can be unsafe

For loops that use i++ do not need to use safemath for this operation because the loop would run out of gas long before this point. Making this addition operation unsafe using unchecked saves gas.

Sample code to make the for loop increment unsafe
```
for (uint i = 0; i < length; i = unchecked_inc(i)) {
    // do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
    unchecked {
        return i + 1;
    }
}
```

Idea borrowed from
https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked

Locations where this was found include
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L443

## Recommended Mitigation Steps

Make the increment in for loops unsafe to save gas

# [G-05] Use iszero assembly for zero checks

Comparing a value to zero can be done using the `iszero` EVM opcode. This can save gas

Source from t11s https://twitter.com/transmissions11/status/1474465495243898885

Locations where this was found include
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L160
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L152
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L167
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L197
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L213
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L275
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L277
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L308
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L103
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L126

## Recommended Mitigation Steps

Use the assembly `iszero` evm opcode to compare values to zero

# [G-06] Save gas with unchecked

Use unchecked math when there is no overflow risk to save gas. Solidity 0.8.X adds Safemath by default, and there are cases where Safemath provides no benefit and uses extra gas.

Locations where this was found include
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L283

## Recommended Mitigation Steps

Add unchecked around math that can't overflow for gas savings. In Solidity before 0.8.0, use the normal math operators instead of safe math functions.

# [G-07] Add payable to functions that won't receive ETH

Identifying a function as payable saves gas. Functions that have a modifier like onlyOwner cannot be called by normal users and will not mistakenly receive ETH. These functions can be payable to save gas.

There are many functions that have the onlyOwner modifier in the contracts. Some examples are
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L225

## Recommended Mitigation Steps

Add payable to these functions for gas savings

# [G-08] Use internal function in place of modifier

An internal function can save gas vs. a modifier. A modifier inlines the code of the original function but an internal function does not.

Source https://blog.polymath.network/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size-c44580b218e6#dde7

Locations where this was found include
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/RewardsFactory.sol#L52
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L60
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L63
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L71
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L79
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L87
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L95
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L73
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L83
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L129

## Recommended Mitigation Steps

Use internal functions in place of modifiers to save gas.

# [G-09] Use uint not bool

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

Locations where this was found include
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L336
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L52
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L277

## Recommended Mitigation Steps

Replace bool variables with uints

# [G-10] Use Solidity errors instead of require

Solidity errors introduced in version 0.8.4 can save gas on revert conditions
https://blog.soliditylang.org/2021/04/21/custom-errors/
https://twitter.com/PatrickAlphaC/status/1505197417884528640

Locations where this was found include
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L96
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L119
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L202
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L217
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L226
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L165
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L187
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L91
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L116
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L23
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L24
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L25
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L28
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L98
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L99
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L103
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L121
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L122
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/oracles/PegOracle.sol#L126

## Recommended Mitigation Steps

Replace require blocks with new solidity errors described in https://blog.soliditylang.org/2021/04/21/custom-errors/

# [G-11] Use simple comparison in if statement

The comparison operators >= and <= use more gas than >, <, or ==. Replacing the  >= and ≤ operators with a comparison operator that has an opcode in the EVM saves gas

The existing code is 
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L189
```
if (block.timestamp >= periodFinish) {
    rewardRate = reward.div(rewardsDuration);
} else {
    uint256 remaining = periodFinish.sub(block.timestamp);
    uint256 leftover = remaining.mul(rewardRate);
    rewardRate = reward.add(leftover).div(rewardsDuration);
}
```

A simple comparison can be used for gas savings by reversing the logic
```
if (block.timestamp < periodFinish) {
    uint256 remaining = periodFinish.sub(block.timestamp);
    uint256 leftover = remaining.mul(rewardRate);
    rewardRate = reward.add(leftover).div(rewardsDuration);
} else {
    rewardRate = reward.div(rewardsDuration);
}
```

## Recommended Mitigation Steps

Replace the comparison operator and reverse the logic to save gas using the suggestions above

# [G-12] Non-public variables save gas

Many constant variables are public, but changing the visibility of these variables to private or internal can save gas.

Locations where this was found include
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L16
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L34
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/rewards/StakingRewards.sol#L35
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L34
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Vault.sol#L36
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/SemiFungibleVault.sol#L19
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L11
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/Controller.sol#L12
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L12
https://github.com/code-423n4/2022-09-y2k-finance/tree/main/src/VaultFactory.sol#L13

## Recommended Mitigation Steps

Declare some public variables as private or internal to save gas

# [G-13] Write contracts in vyper

The contracts are all written entirely in solidity. Writing contracts with vyper instead of solidity can save gas.

Source https://twitter.com/eiber_david/status/1515737811881807876
doggo demonstrates https://twitter.com/fubuloubu/status/1528179581974417414?t=-hcq_26JFDaHdAQZ-wYxCA&s=19

## Recommended Mitigation Steps

Write some or all of the contracts in vyper to save gas