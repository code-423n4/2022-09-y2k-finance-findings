## Low 

## [L-1] Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom
It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

Reference: This [similar medium-severity finding](https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call) from Consensys Diligence Audit of Fei Protocol.
```solidity
Vault.sol:167:        asset.transferFrom(msg.sender, address(this), shares);
Vault.sol:190:        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
Vault.sol:228:        asset.transfer(treasury, feeValue);
Vault.sol:231:        asset.transfer(receiver, entitledShares);
Vault.sol:365:        asset.transfer(_counterparty, idFinalTVL[id]);
```



## Non critical 

## [N - 1] try making public function internal if they are not use outside of contract. 
```solidity
Vault.sol:430:    function epochsLength() public view returns (uint256) {
```

## [N -2] Grammar and typo
```solidity
rewards/StakingRewards.sol:228:            "Previous rewards period must be complete before changing the duration for the new period"
``` 
to `previous rewards period must be completed before changing the duration for the new period`
