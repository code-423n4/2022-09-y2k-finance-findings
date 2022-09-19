# Report

## Non-Critical Issues ##

### [N-01]: Constants instead of unknown variables 
**Context:**

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L168

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L177

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L299 

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L266

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L311

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L68

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L70

+ https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L78

**Description:**

Use constant variables to make the code easier to understand and maintain.

**Recommendation:**

Define constants instead of unknown variables.

### [N-02]: NatSpec is missing

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol

### [N-03]: Public function can be external
**Context:** 

+ [RewardsFactory.getHashedIndex](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L145)

+ [Controller.triggerDepeg](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L148)

+ [Controller.triggerEndEpoch](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L198)

+ [SemiFungibleVault.deposit](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L85)

+ [SemiFungibleVault.previewMint](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L189)

+ [SemiFungibleVault.previewRedeem](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L221)

+ [SemiFungibleVault.maxDeposit](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L237)

+ [SemiFungibleVault.maxMint](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L244)

+ [SemiFungibleVault.maxWithdraw](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L251)

+ [SemiFungibleVault.maxRedeem](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L263)

+ [Vault.changeTreasury](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L277)

+ [Vault.changeTimewindow](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L287)

+ [Vault.changeController](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L295)

+ [Vault.createAssets](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L307)

+ [VaultFactory.createNewMarket](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L178)

**Description:**

Public functions can be declared external if they are not called by the contract.

**Recommendation:**

Declare these functions as external instead of public.