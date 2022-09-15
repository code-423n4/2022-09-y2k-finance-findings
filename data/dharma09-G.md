## 1.DEFAULT VALUE INITIALIZATION

### PROBLEM
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

### PROOF OF CONCEPT
Instances include:

### Vault.sol
```
Vault.sol#L443 :  for (uint256 i = 0; i < epochsLength(); i++) 

StakingRewards.sol#L36 : uint256 public periodFinish = 0;

VaultFactory.sol#L159 :  marketIndex = 0;
```
## 2.OR CONDITIONS COST LESS THAN THEIR EQUIVALENT, AND CONDITIONS (“NOT(SOMETHING IS FALSE)” COSTS LESS THAN “EVERYTHING IS TRUE”)
the equivalent of `(a && b)` is ` !(!a || !b)`

Even with the 10k Optimizer enabled, `OR` conditions cost less than their equivalent `AND` conditions.

### Proof of Concept.
 Compare in Remix this example contract’s 2 diffs (or any test contract of your choice, as experimentation always shows the same results).
```
pragma solidity 0.8.13;
contract Test {
    bool isOpen;
    bool channelPreviouslyOpen;

    function boolTest() external view returns (uint) {
-       if (isOpen && !channelPreviouslyOpen) {
+       if (!(!isOpen || channelPreviouslyOpen)) {
            return 1;
-       } else if (!isOpen && channelPreviouslyOpen) {
+       } else if (!(isOpen || !channelPreviouslyOpen)) {
            return 2;
        }
    }
    function setBools(bool _isOpen, bool _channelPreviouslyOpen) external {
        isOpen = _isOpen;
        channelPreviouslyOpen= _channelPreviouslyOpen;
    }
}
```
 effectively saving 12 gas.

### Affected Code

[Vault.sol#L215](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L215)

Use   ` if !(msg.sender == owner || isApprovedForAll(owner, receiver) != false)` instead of `if(msg.sender != owner && isApprovedForAll(owner, receiver) == false)`

[Vault.sol#L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L96)

Use  `if!(!(block.timestamp < id) || idDepegged[id] != false)` instead of  `if((block.timestamp < id) && idDepegged[id] == false)`

