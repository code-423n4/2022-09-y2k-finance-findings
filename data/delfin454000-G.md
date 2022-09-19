### `Require` revert string is too long
The revert strings below can be shortened to 32 characters or fewer (as shown) to save gas (or else consider using custom error codes, which could save even more gas)
___
[PegOracle.sol: L23](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L23)
```solidity
        require(_oracle1 != address(0), "oracle1 cannot be the zero address");
```
Change message to `oracle1 cannot be the 0 address`
___
[PegOracle.sol: L24](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/oracles/PegOracle.sol#L24)
```solidity
        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
```
Change message to `oracle2 cannot be the 0 address`
___
[StakingRewards.sol: L217-220](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L217-L220)
```solidity
        require(
            tokenAddress != address(stakingToken),
            "Cannot withdraw the staking token"
        );
```
Change message to `Cannot withdraw staking token`
___
The revert strings below are also longer than 32 characters but it's not clear how to shorten them:
___
[SemiFungibleVault.sol: L116-119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/SemiFungibleVault.sol#L116-L119)
```solidity
        require(
            msg.sender == owner || isApprovedForAll(owner, receiver),
            "Only owner can withdraw, or owner has approved receiver for all"
        );
```
___
[StakingRewards.sol: L226-229](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L226-L229)
```solidity
        require(
            block.timestamp > periodFinish,
            "Previous rewards period must be complete before changing the duration for the new period"
        );
```
___
___

### Use `!= 0` instead of `> 0` in a `require` statement if variable is an unsigned integer (`uint`) 
`!= 0` should be used where possible since `> 0` costs more gas
___
[Vault.sol: L187](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L187)
```solidity
        require(msg.value > 0, "ZeroValue");
```
Change to `require(msg.value != 0, "ZeroValue");`  
___
[StakingRewards.sol: L119](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L119)
```solidity
        require(amount > 0, "Cannot withdraw 0");
```
Change to `require(amount != 0, "Cannot withdraw 0");`  
___
___

### State variables should not be initialized to their default values
Initializing `uint` variables to their default value of `0` is unnecessary and costs gas
___
[StakingRewards.sol: L36](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/rewards/StakingRewards.sol#L36)
```solidity
    uint256 public periodFinish = 0;
```
Change to `uint256 public periodFinish;`  
___
___

### Use `++i` instead of `i++` to increase count in a `for` loop
Since use of  `i++` (or equivalent counter) costs more gas, it is better to use `++i` 
___
[Vault.sol: L443-450](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443-L450)
```solidity
        for (uint256 i = 0; i < epochsLength(); i++) {
            if (epochs[i] == _epoch) {
                if (i == epochsLength() - 1) {
                    return 0;
                }
                return epochs[i + 1];
            }
        }
```
Suggestion:
```solidity
        for (uint256 i = 0; i < epochsLength(); ++i) {
            if (epochs[i] == _epoch) {
                if (i == epochsLength() - 1) {
                    return 0;
                }
                return epochs[i + 1];
            }
        }
```
___
___

### Avoid use of default "checked" behavior in a `for` loop
Underflow/overflow checks are made every time `++i` (or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. Therefore, use `unchecked {++i}` instead
___
[Vault.sol: L443-450](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L443-L450)
```solidity
        for (uint256 i = 0; i < epochsLength(); i++) {
            if (epochs[i] == _epoch) {
                if (i == epochsLength() - 1) {
                    return 0;
                }
                return epochs[i + 1];
            }
        }
```
Suggestion:
```solidity
        for (uint256 i = 0; i < epochsLength();) {
            if (epochs[i] == _epoch) {
                if (i == epochsLength() - 1) {
                    return 0;
                }
                return epochs[i + 1];

                unchecked {
                  ++i;
              }
            }
        }
```
___
___

### Inline a function/modifier that’s only used once
It costs less gas to inline the code of functions that are only called once. Doing so saves the cost of allocating the function selector, and the cost of the jump when the function is called.
___
[Vault.sol: L87-91](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L87-L91)
```solidity
    modifier epochHasNotStarted(uint256 id) {
        if(block.timestamp > idEpochBegin[id] - timewindow)
            revert EpochAlreadyStarted();
        _;
    }
```
Since `epochHasNotStarted()` is used only once in this contract (in `function deposit()`) as shown below, it should be inlined to save gas

[Vault.sol: L152-162](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L152-L162)
```solidity
    function deposit(
        uint256 id,
        uint256 assets,
        address receiver
    )
        public
        override
        marketExists(id)
        epochHasNotStarted(id)
        nonReentrant
        returns (uint256 shares)
```
___
[Vault.sol: L95-99](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L95-L99)
```solidity
    modifier epochHasEnded(uint256 id) {
        if((block.timestamp < id) && idDepegged[id] == false)
            revert EpochNotFinished();
        _;
    }
```
Since `epochHasEnded()` is used only once in this contract (in `function withdraw()`) as shown below, it should be inlined to save gas

[Vault.sol: L203-213](https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/Vault.sol#L203-L213)
```solidity
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
```
___
___