1.  Declaring `uint256 i` save gas 

Declaring `uint256 i = 0;` means doing an MSTORE of the value 0 Instead you could just declare `uint256 i` to declare the variable without assigning it’s default value, saving 3 gas per declaration

POC :

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

2. != is more efficient than > in require 

`!= 0` cost less gas compare to `> 0` for unsigned integers in require statement with optimizer enable save 6 gas

because for uint the minimum value would be 0 and never a negative value, that mean check `> 0` is essentially checking that the value is not 0.

resource : https://twitter.com/gzeon/status/1485428085885640706

i suggest changing `> 0` to `!= 0` 

POC :

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119

3.  `require()`/`revert()` strings longer than 32 bytes cost extra gas

Each extra chunk of bytes past the original 32 which costs 3 gas.

resource : https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings

POC :

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L118
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L228

4. cheaper for loops

You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

```
for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
          // Do the thing
          // Unchecked pre-increment is cheapest
          unchecked { ++i; }
  }     
```

POC :

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

5. Use Custom Error instead of Revert / Require String to Save Gas

Custom error from solidity 0.8.4 are cheaper than revert strings, custom error are defined using the `error` statement can use inside and outside the contract.

source https://blog.soliditylang.org/2021/04/21/custom-errors/

i suggest replacing revert / require error strings with custom error.

POC :

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L23
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L24
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L25
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L30
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L98
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L101
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L103
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L121
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L124
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L126
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L91
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L118
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L96
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L204
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L219
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L228


6. use `abi.encodepacked` for gas optimization

Changing the `abi.encode` function to `abi.encodePacked` can save gas since the `abi.encode` function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.

POC :

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L118
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L150	