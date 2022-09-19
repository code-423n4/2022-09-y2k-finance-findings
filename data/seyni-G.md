# GAS

## [G-01] Use custom errors instead of revert string

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met).

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol

```solidity
091:        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
116:        require(
            msg.sender == owner || isApprovedForAll(owner, receiver),
            "Only owner can withdraw, or owner has approved receiver for all"
        );
```

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol

```solidity
023:        require(_oracle1 != address(0), "oracle1 cannot be the zero address");
024:        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
025:        require(_oracle1 != _oracle2, "Cannot be same Oracle");
028:        require(
                (priceFeed1.decimals() == priceFeed2.decimals()),
                "Decimals must be the same"
            );
098:        require(price1 > 0, "Chainlink price <= 0");
099:        require(
                answeredInRound1 >= roundID1,
                "RoundID from Oracle is outdated!"
            );
103:        require(timeStamp1 != 0, "Timestamp == 0 !");
121:        require(price2 > 0, "Chainlink price <= 0");
122:        require(
                answeredInRound2 >= roundID2,
                "RoundID from Oracle is outdated!"
            );
126:        require(timeStamp2 != 0, "Timestamp == 0 !");
```

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol

```solidity
096:        require(amount != 0, "Cannot stake 0");
119:        require(amount > 0, "Cannot withdraw 0");
202:        require(
                rewardRate <= balance.div(rewardsDuration),
                "Provided reward too high"
            );
217:        require(
                tokenAddress != address(stakingToken),
                "Cannot withdraw the staking token"
            );
226:        require(
                block.timestamp > periodFinish,
                "Previous rewards period must be complete before changing the duration for the new period"
            );
```

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```solidity
165:        require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
187:        require(msg.value > 0, "ZeroValue");
```

## [G-02] Long error message

Reducing the size of errors decrease deployment cost and runtime cost when the revert condition is met.

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol

```solidity
116:        require(
                msg.sender == owner || isApprovedForAll(owner, receiver),
                "Only owner can withdraw, or owner has approved receiver for all"
            );
```

## [G-03] `i++` instead of `++i` and inline `unchecked{++i}` could be used

`i++` cost 5 more gas per iteration than `++i` and the increment could be unchecked.

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```solidity
443:        for (uint256 i = 0; i < epochsLength(); i++) {
```

## [G-04] Useless explicit initialization of variables to default value

Explicitly initializing a variable with its default value wastes gas.

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol

```solidity
036:    uint256 public periodFinish = 0;
```

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```solidity
443:        for (uint256 i = 0; i < epochsLength(); i++) {
```

## [G-05] `epochsLength()` should be cached

`epochsLength()` is called twice per loop :

- when checking if the condition `i < epochsLength()` is true

- when checking if the condition `i == epochsLength() - 1` is true

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```solidity
443:        for (uint256 i = 0; i < epochsLength(); i++) {
445:        if (i == epochsLength() - 1) {
```

## [G-06] Boolean comparison

Using `if(bool)` or `if(!bool)` instead of `if(bool == true)` or `if(bool == false)` cost less gas.

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol

```solidity
093:        if(vault.idExists(epochEnd) == false)
211:        if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
```

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol

```solidity
096:        if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
```

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```solidity
096:        if((block.timestamp < id) && idDepegged[id] == false)
215:        if(
                msg.sender != owner &&
                isApprovedForAll(owner, receiver) == false)
```

File : https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```solidity
314:        if(idExists[epochEnd] == true)
```

## [G-07] `EMPTY` variable is not consistently used and use more gas

Using the variable `EMPTY` use more deployment and runtime gas than hardcoding `""`.

And the variable is not always used (see below).

File :
https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol

```solidity
069:    ) ERC1155("") {
```