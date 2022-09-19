# [G-01] Prefix increment costs less gas than postfix increment

There's 1 instance of this issue.

```
File: src/Vault.sol
443: for (uint256 i = 0; i < epochsLength(); i++) {
```

# [G-02] The increment in for loop post condition can be made unchecked to save gas

There's 1 instance of this issue.

```
File: src/Vault.sol
443: for (uint256 i = 0; i < epochsLength(); i++) {
```

# [G-03] Initializing a variable with the default value wastes gas

There's 1 instance of this issue.

```
File: src/Vault.sol
443: for (uint256 i = 0; i < epochsLength(); i++) {
```

# [G-04] Use != 0 instead of > 0 to save gas.

Replace `> 0` with `!= 0` for unsigned integers.

There are 2 instances of this issue.

```
File: src/rewards/StakingRewards.sol
119: require(amount > 0, "Cannot withdraw 0");
134: if (reward > 0) {
```

# [G-05] Use custom errors rather than require/revert strings to save gas

There are 12 instances of this issue.

```
File: src/SemiFungibleVault.sol
91: require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
```

```
File: src/Vault.sol
165: require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
187: require(msg.value > 0, "ZeroValue");
```

```
File: src/oracles/PegOracle.sol
23: require(_oracle1 != address(0), "oracle1 cannot be the zero address");
24: require(_oracle2 != address(0), "oracle2 cannot be the zero address");
25: require(_oracle1 != _oracle2, "Cannot be same Oracle");
98: require(price1 > 0, "Chainlink price <= 0");
103: require(timeStamp1 != 0, "Timestamp == 0 !");
121: require(price2 > 0, "Chainlink price <= 0");
126: require(timeStamp2 != 0, "Timestamp == 0 !");
```

```
File: src/rewards/StakingRewards.sol
96: require(amount != 0, "Cannot stake 0");
119: require(amount > 0, "Cannot withdraw 0");
```

# [G-06] Long revert strings are gas wasteful

Custom errors and NATSPEC comments can also provide rich information and are more gas efficient.

There are 4 instances of this issue.

```
118: "Only owner can withdraw, or owner has approved receiver for all"
```

```
File: src/oracles/PegOracle.sol
23: require(_oracle1 != address(0), "oracle1 cannot be the zero address");
24: require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```

```
File: src/rewards/StakingRewards.sol
228: "Previous rewards period must be complete before changing the duration for the new period"
```

# [G-07] Don’t compare boolean expressions to boolean literals

There are 6 instances of this issue.

```
File: src/Controller.sol
93: if(vault.idExists(epochEnd) == false)
211: if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
```

```
File: src/Vault.sol
96: if((block.timestamp < id) && idDepegged[id] == false)
217: isApprovedForAll(owner, receiver) == false)
314: if(idExists[epochEnd] == true)
```

```
File: src/rewards/RewardsFactory.sol
96: if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
```

# [G-08] Using private rather than public for constants, saves gas

The values can still be inspected on the source code if necessary.

There's 1 instance of this issue.

```
File: src/Controller.sol
16: uint256 public constant VAULTS_LENGTH = 2;
```

# [G-09] x += y costs more gas than x = x + y for state variables

There's 1 instance of this issue.

```
File: src/VaultFactory.sol
195: marketIndex += 1;
```
