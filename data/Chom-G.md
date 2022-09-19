## Use custom errors instead of require

There are many parts in the code still using require

Vault.sol

```
165: require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
187: require(msg.value > 0, "ZeroValue");
```

Should be rewritten to use custom errors such as

```
if (msg.value == 0) revert ZeroValue()
```

## getNextEpoch should use binary search

Currently, getNextEpoch is using a linear search, 500 epochs will likely cause a gas limit problem.

https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L438-L451

```solidity
    function getNextEpoch(uint256 _epoch)
        public
        view
        returns (uint256 nextEpochEnd)
    {
        for (uint256 i = 0; i < epochsLength(); i++) {
            if (epochs[i] == _epoch) {
                if (i == epochsLength() - 1) {
                    return 0;
                }
                return epochs[i + 1];
            }
        }
    }
```

If using binary search, will reduce gas usage in case there are many epochs. 500 epochs can be done without any issue.