# Gas Report

- [G-01: use unchecked when increment loop iterator](#g-01-use-unchecked-when-increment-loop-iterator)
- [G-02: use ++i instead of i++](#g-02-use-i-instead-of-i)
- [G-03: precompute hash and save as constant value](#g-03-precompute-hash-and-save-as-constant-value)
- [G-04: precompute hash and save as immutable value](#g-04-precompute-hash-and-save-as-immutable-value)
- [G-05: overwrite `Vault.previewWithdraw()`](#g-05-overwrite-vaultpreviewwithdraw)

## G-01: use unchecked when increment loop iterator

Since it can't realistically overflow, you can increment it within an `unchecked{}` block

```
./src//Vault.sol:443:        for (uint256 i = 0; i < epochsLength(); i++) {
```

## G-02: use ++i instead of i++

It's a little cheaper

```
./src//Vault.sol:443:        for (uint256 i = 0; i < epochsLength(); i++) {
```

## G-03: precompute hash and save as constant value

Instead of hashing the hardcoded `"rY2K"` string in each call, you can precompute the value and save it as a constant. That should save you a lot of gas

```
./src//Vault.sol:389:            keccak256(abi.encodePacked("rY2K"))
```

## G-04: precompute hash and save as immutable value

In the same line you also hash the vault's `symbol`. You can do the same thing as above but instead, save the value as an immutable state variable. That way you don't have to pass any extra parameters when initializing the contract

```
./src//Vault.sol:388:            keccak256(abi.encodePacked(symbol)) ==
```

## G-05: overwrite `Vault.previewWithdraw()`

The `previewWithdraw()` function is called in each `withdraw()` tx. 

```sol
    function previewWithdraw(uint256 id, uint256 assets)
        public
        view
        virtual
        returns (uint256)
    {
        uint256 supply = totalSupply(id); // Saves an extra SLOAD if totalSupply is non-zero.

        return supply == 0 ? assets : assets.mulDivUp(supply, totalAssets(id));
    }
```

In the Vault contract, you overwrite the `totalAssets()` function to return the value of `totalSupply()`.

```sol
    function totalAssets(uint256 _id)
        public
        view
        override
        marketExists(_id)
        returns (uint256)
    {
        return totalSupply(_id);
    }
```

Thus, the `previewWithdraw()` function always returns `assets`. Instead of running through all that computation to just return `assets`, you can overwrite the function in the Vault contract to just do that. That should save you a lot of gas:

```
    function previewWithdraw(uint256 id, uint256 assets)
        public
        view
        override
        returns (uint256)
    {
        return assets;
    }
```