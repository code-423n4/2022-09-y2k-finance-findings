1.
Title: Set as `immutable` can save gas

Proof of Concept:
[RewardsFactory.sol#L9-L11](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L9-L11)
[PegOracle.sol#L10-L11](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L10-L11)

Recommended Mitigation Steps:
can be set as immutable, which already set once in the constructor
________________________________________________________________________

2.
Title: abi.encode() is less efficient than abi.encodePacked()

Proof of Concept:
[RewardsFactory.sol#L150](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L150)
________________________________________________________________________

3.
Title: Using SafeMath for solidity >0.8

Proof of Concept:
[StakingRewards.sol#L29](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L29)

Recommended Mitigation Steps:
it's better to remove `using SafeMath for uint256` for solidity >0.8
reference: https://github.com/OpenZeppelin/openzeppelin-contracts/issues/2465

________________________________________________________________________

4.
Title: Default value initialization

Impact:
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

Proof of Concept:
[StakingRewards.sol#L36](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L36)
[Vault.sol#L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443)

Recommended Mitigation Steps:
Remove explicit initialization for default values.
________________________________________________________________________

5.
Title: Using `!=` is more gas efficient in `require` statement

Proof of Concept:
[StakingRewards.sol#L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119)
[PegOracle.sol#L98](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L98)
[PegOracle.sol#L121](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L121)
[Vault.sol#L187](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187)

Recommended Mitigation Steps:
Change from `> 0` to `!= 0`
________________________________________________________________________

6.
Title: Comparison operators

Proof of Concept:
[StakingRewards.sol#L189](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L189)

Recommended Mitigation Steps:
Replace `<=` with `<`, and `>=` with `>` for gas optimization
________________________________________________________________________

7.
Title: Custom errors from Solidity 0.8.4 are cheaper than revert strings

Impact:
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information

Custom errors are defined using the error statement
reference: https://blog.soliditylang.org/2021/04/21/custom-errors/

Proof of Concept:
[StakingRewards.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol) (various line)
[PegOracle.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol) (various line)
[SemiFungibleVault.sol#L118](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L118)

Recommended Mitigation Steps:
Replace require statements with custom errors.
________________________________________________________________________

8.
Title: Reduce the size of error messages (Long revert Strings)

Impact:
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

Proof of Concept:
[StakingRewards.sol#L228](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L228)
[SemiFungibleVault.sol#L118](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L118)

Recommended Mitigation Steps:
Consider shortening the revert strings to fit in 32 bytes
________________________________________________________________________

9.
Title: Consider make constant as private to save gas

Proof of Concept:
[Controller.sol#L16](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L16)

Recommended Mitigation Steps:
I suggest changing the visibility from `public` to `internal` or `private`
________________________________________________________________________

10.
Title: Using `== false` cost more gas

Proof of Concept:
[Controller.sol#L93](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L93)
[Vault.sol#L217](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L217)
[Controller.sol#L211](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L211)

Recommended Mitigation Steps:
Change to:

```
    require(!vault.idExists(epochEnd));
```
________________________________________________________________________

11.
Title: Using `*=` can save gas

Proof of Concept:
[PegOracle.sol#L74](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L74)
[Controller.sol#L300](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L300)

Recommended Mitigation Steps:
Change to:

```
	nowPrice *= decimals10;
```
________________________________________________________________________

12.
Title: Using unchecked and prefix increment is more effective for gas saving:

Proof of Concept:
[Vault.sol#L443](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443)

Recommended Mitigation Steps:
Change to:

```
	for (uint256 i = 0; i < epochsLength();) {
  	// ...
	unchecked { ++i; }
	}
```
________________________________________________________________________