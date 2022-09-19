## **L001 - Unsafe ERC20 Operation(s)**

### **Description**

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's `SafeERC20` library or at least to wrap each operation in a `require` statement.

To circumvent ERC20's `approve` functions race-condition vulnerability use OpenZeppelin's `SafeERC20` library's `safe{Increase|Decrease}Allowance` functions.

In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.

```solidity
asset.transferFrom(msg.sender, address(this), shares);
```

[https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L167](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L167)

