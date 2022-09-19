### G-01 DONT COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol

```
93: if(vault.idExists(epochEnd) == false)

211: if(insrVault.idExists(epochEnd) == false || riskVault.idExists(epochEnd) == false)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```
80: if(idExists[id] != true)

96: if((block.timestamp < id) && idDepegged[id] == false)

217: isApprovedForAll(owner, receiver) == false)

314: if(idExists[epochEnd] == true)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol

```
96: if(Vault(_insrToken).idExists(_epochEnd) == false || Vault(_riskToken).idExists(_epochEnd) == false)
```



### G-02 UNCHECKING ARITHMETICS OPERATIONS THAT CANT UNDERFLOW or OVERFLOW

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block: [Solidity Doc](https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic)

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol

```
283: uint256 timeSinceUp = block.timestamp - startedAt;

299: uint256 decimals = 10**(18-(priceFeed.decimals()))
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```
88: if(block.timestamp > idEpochBegin[id] - timewindow)

227: entitledShares = entitledShares - feeValue;

266: return (amount * epochFee[_epoch]) / 1000;

443: for (uint256 i = 0; i < epochsLength(); i++)

445: if (i == epochsLength() - 1)

```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L399-L404

```
entitledAmount =

                    amount.divWadDown(idFinalTVL[id]).mulDivDown(

                        idClaimTVL[id],

                        1 ether

                    ) +

                    amount;
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195

```
195: marketIndex += 1;
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L73

```
73: int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));
```

Mitigation : Consider wrapping with an `unchecked` block

### G-03 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol

```
264: returns (int256 nowPrice)

```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```
162: returns (uint256 shares)

185: returns (uint256 shares)

213: returns (uint256 shares)

263: returns (uint256 feeValue)

381: returns (uint256 entitledAmount)

411: returns (uint256 nextEpochEnd)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol

```
186: public onlyAdmin returns (address insr, address rsk)

388: returns (address[] memory vaults)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol

```
89: function getOracle1_Price() public view returns (int256 price)

112: function getOracle2_Price() public view returns (int256 price)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol

```
86: returns (address insr, address risk)

148: returns (bytes32 hashedIndex)
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L46-L55

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
```


### G-04 ARRAY.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR- LOOP

Reading array length at each iteration of the loop consumes more gas than necessary.

In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Here, Consider storing the array’s length in a variable before the for-loop, and use this new variable instead.

Here, `epochsLength()` function essentially returns `epochs.length`

Wrong Implementation:
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

```
for (uint256 i = 0; i < epochsLength(); i++)
```

`epochsLength()` can be cached before being used in the `for` loop


### G-05 ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 (SAME FOR --I VS I-- OR I -= 1)

Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

-   `i += 1` is the most expensive form
-   `i++` costs 6 gas less than `i += 1`
-   `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

-   `i -= 1` is the most expensive form
-   `i--` costs 11 gas less than `i -= 1`
-   `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)


Consider using pre-increments and pre-decrements where they are relevant (meaning: not where post-increments/decrements logic are relevant).

**Note** : the above is true for for loops as well 

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

```
for (uint256 i = 0; i < epochsLength(); i++)
```

should be

```
for (uint256 i = 0; i < epochsLength(); ++i)
```


### G-06 IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

```
for (uint256 i = 0; i < epochsLength(); i++) {
```

should be

```
for (uint256 i = 0; i < epochsLength(); i++) {
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L159

```
marketIndex = 0;
```

should not be initialized to 0 in the constructor


https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L36

```
uint256 public periodFinish = 0;
```

should be

```
uint256 public periodFinish;
```

Consider removing explicit initializations for default values.


### G-07 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195

affected code:

```
 marketIndex += 1;
```

should be replaces with

```
 marketIndex = marketIndex+1;
```


### G-08 USING `> 0` COSTS MORE GAS THAN `!= 0` WHEN USED ON A `UINT` IN A `REQUIRE()` STATEMENT

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L187

```
require(msg.value > 0, "ZeroValue");
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L119

```
 require(amount > 0, "Cannot withdraw 0");
```


### G-09 USE CUSTOM ERRORS RATHER THAN REQUIRE() STRINGS TO SAVE GAS

Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they’re hitby [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol

```
91: require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L110-L119

```
 function withdraw(

        uint256 id,

        uint256 assets,

        address receiver,

        address owner

    ) external virtual returns (uint256 shares) {

        require(

            msg.sender == owner || isApprovedForAll(owner, receiver),

            "Only owner can withdraw, or owner has approved receiver for all"

        );
```


https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol

```
165: require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");

187: require(msg.value > 0, "ZeroValue");
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol

```
23: require(_oracle1 != address(0), "oracle1 cannot be the zero address");

24: require(_oracle2 != address(0), "oracle2 cannot be the zero address");

25: require(_oracle1 != _oracle2, "Cannot be same Oracle");

98: require(price1 > 0, "Chainlink price <= 0");

99: require(answeredInRound1 >= roundID1,"RoundID from Oracle is outdated!"      ); 

103: require(timeStamp1 != 0, "Timestamp == 0 !");

121:  require(price2 > 0, "Chainlink price <= 0");

122: require(answeredInRound2 >= roundID2,"RoundID from Oracle is outdated!"       );

126: require(timeStamp2 != 0, "Timestamp == 0 !");
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol

```
96: require(amount != 0, "Cannot stake 0");

119: require(amount > 0, "Cannot withdraw 0");

202: require(rewardRate <= balance.div(rewardsDuration),"Provided reward too high");

217:  require(tokenAddress != address(stakingToken),"Cannot withdraw the staking token");

226:  require(block.timestamp > periodFinish,"Previous rewards period must be complete before changing the duration for the new period");

```

### G-10 FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2), `DUP1`(3), `ISZERO`(3), `PUSH2`(3), `JUMPI`(10), `PUSH1`(3), `DUP1`(3), `REVERT`(0), `JUMPDEST`(1), `POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

The following funciton in  [SemiFungibleVault.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol)

- `withdraw` 

The following functions in [Vault.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol)

- `changeTreasury`
- `changeTimewindow`
- `changeController`
- `createAssets`
- `setClaimTVL`
- `sendTokens`

The following functions in [VaultFactory.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol)

- `createNewMarket`
- `deployMoreAssets`
- `setController`
- `changeTreasury`
- `changeTimewindow`
- `changeController`
- `changeOracle`

The following functions in [RewardsFactory.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol)

- `createStakingRewards`

The following functions in [StakingRewards.sol](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol)

- `notifyRewardAmount`
- `recoverERC20`
- `setRewardsDuration`


### G-11 EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. 

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L276-L280

```
    function beforeWithdraw(

        uint256 id,

        uint256 assets,

        uint256 shares

    ) internal virtual {}
```

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L282-L286

```
function afterDeposit(

	uint256 id,
	
	uint256 assets,
	
	uint256 shares

) internal virtual {}
```