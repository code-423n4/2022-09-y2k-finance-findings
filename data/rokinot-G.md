### Solmate's reentrancy guard is cheaper than OZ's.

Since the project is already using some solmate libraries, it may save some gas by using their reentrancy guard as well.

https://github.com/transmissions11/solmate/blob/main/src/utils/ReentrancyGuard.sol

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L10