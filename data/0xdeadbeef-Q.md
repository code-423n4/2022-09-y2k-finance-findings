# audit report for y2k_finance--low risk reports
by 0xdeadbeef

## In the file Controller.sol: 
these address variables are not checked whether they are address(0), address variable should check if it is zero
```
	line 90: vaultAddress
	line 168: riskVault
	line 169: insrVault
	line 261: _token
```
## In the file SemiFungibleVault.sol: 
Line 96	        _mint(receiver, id, shares, EMPTY): 
 _safemint() should be used instead of _mint() function whereever possible

these address variables are not checked whether they are address(0), address variable should check if it is zero
```
	line 36: caller
	line 37: indexed
	line 36: caller
	line 53: receiver
	line 37: indexed
	line 88: receiver
	line 53: receiver
	line 114: owner
	line 251: owner
	line 263: owner
```
## In the file Vault.sol: 
Line 171	        _mint(receiver, id, shares, EMPTY): 
_safemint() should be used instead of _mint() function whereever possible

these address variables are not checked whether they are address(0), address variable should check if it is zero
```
	line 116: _assetAddress
	line 156: receiver
	line 184: receiver
	line 191: asset
	line 192: asset
	line 209: receiver
	line 210: owner
	line 365: _counterparty
```
the assert() function when false, uses up all the remaining gas and reverts all the changes made.
Line 192:	        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));



## In the file VaultFactory.sol: 
these address variables are not checked whether they are address(0), address variable should check if it is zero
```
	line 186: insr
	line 219: risk
	line 227: hedge
	line 228: risk
	line 238: hedge
	line 260: hedge
	line 261: risk
```

## In the file Owned.sol: 
these address variables are not checked whether they are address(0), address variable should check if it is zero
```
	line 8: newOwner
	line 9: oldOwner
```

## In the file RewardsDistributionRecipient.sol: 
these address variables are not checked whether they are address(0), address variable should check if it is zero
```
	line 20: _rewardsDistribution
```

## In the file RewardsFactory.sol: 
these address variables are not checked whether they are address(0), address variable should check if it is zero
```
	line 42: hedgeFarm
	line 43: riskFarm
	line 64: _govToken
	line 65: _factory
	line 66: _admin
	line 90: _insrToken
	line 120: insrStake
	line 121: riskStake
	line 120: insrStake
	line 121: riskStake
	line 137: insrStake
```

## In the file StakingRewards.sol: 
these address variables are not checked whether they are address(0), address variable should check if it is zero
```
	line 73: _owner
	line 74: _rewardsDistribution
	line 75: _rewardsToken
	line 76: _stakingToken
	line 173: account
	line 213: tokenAddress
	line 218: stakingToken
```