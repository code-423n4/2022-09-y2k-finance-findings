# [L-01] Inaccurate comment
## Targets
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L265
## Description
The comment says: "multiply by 1000 then divide by 5", however, the code does the opposite: it multiplies by a fee and
divides by 1000.

# [L-02] Missing `marketExists` modifier
## Targets
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L350
## Description
To ensure that claim TVL cannot be added to an non-existent market, consider adding the missing modifier.

# [L-03] Missing `epochHasEnded` modifier
## Targets
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L363
## Description
To ensure funds can never be transferred out of the vault until an epoch is over, consider adding the missing modifier.



# [N-01] `Vault` doesn't implement EIP-4626
## Targets
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L12
## Description
`Vault` doesn't implement all the functions from EIP-4626. Specifically, `mint` and `redeem` are missed.

What's more essential is that `Vault`, in its core, is not a vault as it's defined in EIP-4626. The main lacking feature is the conversion
between shares and assets: `Vault` [treats them equally](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L251).
This makes useless the usage of [previewWithdraw](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L220)
and [previewDeposit](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L165) functions because
they always return the input amount:
1. `previewDeposit` calls [convertToShares](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L143),
which converts asset to shares. But since [asset are shares](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L251),
it always returns assets. [supply](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L149)
always equals [totalAssets(id)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L152),
thus the returned values is always `assets`;
1. in `previewWithdraw`, [the logic is the same](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L213).

Tokenized Vaults, as per EIP-4626, need to track assets and shares separately so they could distribute extra assets
(accumulated as accrued fees or direct, not deposited, transfers) pro rata among depositors (shareholders). And `Vault`
does implement this mechanism, but in a different way: it uses the [beforeWithdraw](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L378)
function to calculate the amounts share holders are eligible for after an epoch is finalized. This is the same logic
that's implemented by the shares-to-assets and assets-to-shares functions from EIP-4626.
## Recommended Mitigation Steps
Since this is a non-critical issue, no changes are needed to be made. But the `beforeWithdraw` function is extra code
that adds complexity and that implements what's already there–pro rata distribution of assets to shareholders, which is
provided by the `ERC4626` contract.

# [N-02] `SafeMath` is not required in Solidity >=0.8.0
## Targets
- https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L4
## Description
Solidity 0.8.0 [introduced](https://blog.soliditylang.org/2020/12/16/solidity-v0.8.0-release-announcement/#checked-arithmetic)
the native check for underflows/overflows.