# Gas optimization
### Don't Initialize Variables with Default Value

### Examples:
```
2022-09-y2k-finance\src\Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```

### Cache Array Length Outside of Loop

### Examples:
```
2022-09-y2k-finance\src\Controller.sol::86 => vaultsAddress.length != VAULTS_LENGTH
2022-09-y2k-finance\src\Controller.sol::200 => vaultFactory.getVaults(marketIndex).length != VAULTS_LENGTH)
2022-09-y2k-finance\src\Vault.sol::355 => solhint-disable-next-line max-line-length
2022-09-y2k-finance\src\Vault.sol::428 => /** @notice Lookup total epochs length
2022-09-y2k-finance\src\Vault.sol::431 => return epochs.length;
```

### Use != 0 instead of > 0 for Unsigned Integer Comparison

### Examples:
```
2022-09-y2k-finance\src\Vault.sol::187 => require(msg.value > 0, "ZeroValue");
2022-09-y2k-finance\src\oracles\PegOracle.sol::98 => require(price1 > 0, "Chainlink price <= 0");
2022-09-y2k-finance\src\oracles\PegOracle.sol::121 => require(price2 > 0, "Chainlink price <= 0");
2022-09-y2k-finance\src\rewards\StakingRewards.sol::119 => require(amount > 0, "Cannot withdraw 0");
2022-09-y2k-finance\src\rewards\StakingRewards.sol::134 => if (reward > 0) {
```

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

### Examples:
```
2022-09-y2k-finance\src\Controller.sol::179 => keccak256(
2022-09-y2k-finance\src\Controller.sol::235 => keccak256(
2022-09-y2k-finance\src\Vault.sol::388 => keccak256(abi.encodePacked(symbol)) ==
2022-09-y2k-finance\src\Vault.sol::389 => keccak256(abi.encodePacked("rY2K"))
2022-09-y2k-finance\src\VaultFactory.sol::278 => keccak256(abi.encodePacked(_marketVault.index, _marketVault.epochBegin, _marketVault.epochEnd)),
2022-09-y2k-finance\src\rewards\RewardsFactory.sol::118 => bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));
2022-09-y2k-finance\src\rewards\RewardsFactory.sol::125 => keccak256(
2022-09-y2k-finance\src\rewards\RewardsFactory.sol::150 => return keccak256(abi.encode(_index, _epoch));
```

### Long Revert Strings

### Examples:
```
2022-09-y2k-finance\src\oracles\PegOracle.sol::23 => require(_oracle1 != address(0), "oracle1 cannot be the zero address");
2022-09-y2k-finance\src\oracles\PegOracle.sol::24 => require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```

### Use Prefix Increment instead of Postfix Increment if possible

### Examples:
```
2022-09-y2k-finance\src\Vault.sol::443 => for (uint256 i = 0; i < epochsLength(); i++) {
```


