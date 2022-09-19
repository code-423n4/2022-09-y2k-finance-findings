**Wrong NatSpec comment for `setClaimTVL()` in `Vault.sol`**

[https://github.com/code-423n4/2022-09-y2k-finance/blob/8f41433c1cd866a138e6edbc32b7570fe94643fc/src/Vault.sol#L342](https://github.com/code-423n4/2022-09-y2k-finance/blob/8f41433c1cd866a138e6edbc32b7570fe94643fc/src/Vault.sol#L342)

The NatSpec is saying “Function to be called after endEpoch” but it is called when Controller gets a `triggerDepeg()` call as well.