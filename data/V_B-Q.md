### 1. depositETH double work

There is a `depositETH` function in a `Vault` smart contract. The function accepts ether, deposit it to WETH contract and transfer wrapped tokens to the sender. Later the funds will be transferred as a ERC20 token from users account to smart contract. 

The alternative approach can be just accepting the ether, that which will reduce the cost of depositing a lot and clarify the logic.

### 2. symbol == "rY2K" check

The Vault smart contract has the following check in the `beforeWithdraw` function:

```solidity
if (
    keccak256(abi.encodePacked(symbol)) ==
    keccak256(abi.encodePacked("rY2K"))
) {
    ...
}
```

It makes no sense to do this check every time the function is called, it's better to do this check once in the constructor and store the result as an `immutable` variable.