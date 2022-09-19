# [L-01] Use transfer/safeTransfer consistently

Transfers inside `SemiFungibleVault.sol` and `StakingRewards.sol` are using the `safeTransfer` variant, and transfers inside `Vault.sol` are using the `transfer` variant.

Consider using it only `safeTransfer`, since it will revert even if the token doesn't throw on failure and instead returns false.


# [L-02] Replace assert with require or custom error

```
assert(IWETH(address(asset)).transfer(msg.sender, msg.value)); 
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190

As described on the solidity [docs](https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require). "The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix."

# [L-03] Add validation against address zero for constructor inputs on `StakingRewards.sol`

```
constructor(
    address _owner,
    address _rewardsDistribution,
    address _rewardsToken,
    address _stakingToken,
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L73-L76

# [L-04] Use the latest version of OpenZeppelin

The project is using OpenZeppelin v4.6.0. At the time of this audit, OpenZeppelin latest stable version is v4.7.3. Using the latest version ensures the latest security fixes are present.

# [NC-01] Public function not called by the contract should be declared external

```
function getNextEpoch(uint256 _epoch)
    public
    view
    returns (uint256 nextEpochEnd)
{
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L438-L451

```
function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L198

```
function getOracle1_Price() public view returns (int256 price) { 
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L89

# [NC-02] Incosistent line breaks and curly braces for conditionals

This conditional is breaking a line and it has only 30 characters.

```
if(
    block.timestamp > epochEnd
    )
    revert EpochExpired();
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L105-L108

There are coditional with the same amount of characters in the same contract that are not breaking a line

```
if(_l2Sequencer == address(0))
    revert ZeroAddress();
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L132-L133

There are also coditional with only one statement and with curly braces.

```
if (timeSinceUp <= GRACE_PERIOD_TIME) {
    revert GracePeriodNotOver();
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L284-L286

## Recommendation

Fix a maximum amount of characters to add line breaks on conditional so that it's normalized in the project, and pick one approach for conditionals with only one statement (with or without curly braces).

Running the linter and formatter present in the package.json (solhint/prettier) with the write option will also enforce a consistent coding style. This will facilitate code refactors and project maintainability in the future.

# [NC-03] Use constants and scientific notation

The value `10000` could be saved into a constant since it's reused. Also, prefer the usage of scientific notation for numbers with multiple zeroes. e.g. 1000000 = 1e6, 10000 = 1e4.

```
if (price1 > price2) {
    nowPrice = (price2 * 10000) / price1;
} else {
    nowPrice = (price1 * 10000) / price2;
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L67-L71

```
nowPrice / 1000000,
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L78

# [NC-04] Inconsistent space in comment

```
//Taking fee from the amount
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L225

# [NC-05] Inconsistent use of return named variable

The functions bellow are declaring a return named variable in the function header, and are also returning values manually at the end of the function.

```
function latestRoundData()
    public
    view
    returns (
        uint80 roundID,
        int256 nowPrice,
        uint256 startedAt,
        uint256 timeStamp,
        uint80 answeredInRound
    )
{
    (
        uint80 roundID1,
        int256 price1,
        uint256 startedAt1,
        uint256 timeStamp1,
        uint80 answeredInRound1
    ) = priceFeed1.latestRoundData();

    int256 price2 = getOracle2_Price();

    if (price1 > price2) {
        nowPrice = (price2 * 10000) / price1;
    } else {
        nowPrice = (price1 * 10000) / price2;
    }

    int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
    nowPrice = nowPrice * decimals10;

    return (
        roundID1,
        nowPrice / 1000000,
        startedAt1,
        timeStamp1,
        answeredInRound1
    );
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L46-L83

```
function getOracle1_Price() public view returns (int256 price) {
    (
        uint80 roundID1,
        int256 price1,
        ,
        uint256 timeStamp1,
        uint80 answeredInRound1
    ) = priceFeed1.latestRoundData();

    require(price1 > 0, "Chainlink price <= 0");
    require(
        answeredInRound1 >= roundID1,
        "RoundID from Oracle is outdated!"
    );
    require(timeStamp1 != 0, "Timestamp == 0 !");

    return price1;
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L89-L106

```
function getOracle2_Price() public view returns (int256 price) {
    (
        uint80 roundID2,
        int256 price2,
        ,
        uint256 timeStamp2,
        uint80 answeredInRound2
    ) = priceFeed2.latestRoundData();

    require(price2 > 0, "Chainlink price <= 0");
    require(
        answeredInRound2 >= roundID2,
        "RoundID from Oracle is outdated!"
    );
    require(timeStamp2 != 0, "Timestamp == 0 !");

    return price2;
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L112-L129

```
function withdraw(
    uint256 id,
    uint256 assets,
    address receiver,
    address owner
)
    external
    override
    epochHasEnded(id)
    marketExists(id)
    returns (uint256 shares)
{
    if(
        msg.sender != owner &&
        isApprovedForAll(owner, receiver) == false)
        revert OwnerDidNotAuthorize(msg.sender, owner);

    shares = previewWithdraw(id, assets); // No need to check for rounding error, previewWithdraw rounds up.

    uint256 entitledShares = beforeWithdraw(id, shares);
    _burn(owner, id, shares);

    //Taking fee from the amount
    uint256 feeValue = calculateWithdrawalFeeValue(entitledShares, id);
    entitledShares = entitledShares - feeValue;
    asset.transfer(treasury, feeValue);

    emit Withdraw(msg.sender, receiver, owner, id, assets, entitledShares);
    asset.transfer(receiver, entitledShares);

    return entitledShares;
}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L203-L234

