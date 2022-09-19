# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1      | Immutable state variables lack zero value -address or uint- checks | Low | 3 |
| 2      | `require` should be used instead of `assert` | Low | 1  |
| 3      | Setters should check the input value and revert if it's the zero address or zero | Low | 3  |
| 4      | Redundant ZERO address check when changing controller in `VaultFactory` |  NC | 1 |
| 5      | Misorganized checks in the `createNewMarket` function inside the `VaultFactory` contract | NC | 1 |
| 6      | Named return variables not used anywhere in the functions | NC |11 |
| 7      | `public` functions not called by the contract should be declared `external` instead   | NC | 18 |
| 8      | Use scientific notation for better readability  | NC | 4 |
| 9      | `constant` should be defined rather than using magic numbers  | NC | 2 |


## Findings

### 1- Immutable state variables lack zero value -address or uint- checks  :

Constructors should check the values written in an immutable state variables(address, uint, int) is not the zero value (address(0) or 0)

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: src/Vault.sol

[strikePrice = _strikePrice;](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L135)

File: src/rewards/StakingRewards.sol

[rewardsToken = ERC20(_rewardsToken);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L81)

[stakingToken = IERC1155(_stakingToken);](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L82)

#### Mitigation
Add non-zero value -address or uint/int- checks in the constructors for the instances aforementioned.

### 2- `require` should be used instead of `assert` :

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as `require()/revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: src/Vault.sol

[assert(IWETH(address(asset)).transfer(msg.sender, msg.value));](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190)

#### Mitigation
Replace the `assert` checks aforementioned with `require` statements.

### 3- Setters should check the input value and revert if it's the zero address or zero :

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: src/VaultFactory.sol

[function changeTimewindow(uint256 _marketIndex, uint256 _timewindow)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L327)

File: src/rewards/StakingRewards.sol

[function setRewardsDuration(uint256 _rewardsDuration)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L225)

File: src/rewards/RewardsDistributionRecipient.sol

[function setRewardsDistribution(address _rewardsDistribution)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsDistributionRecipient.sol#L20)

#### Mitigation
Add non-zero checks for uint to the setters aforementioned.

### 4- Redundant ZERO address check when changing controller in `VaultFactory` contract :

The `changeController` function in the `VaultFactory` contract check that the new controller address is not `address(0)` and then calls the `changeController` function from the `Vault` contract which also checks for non-zero address which not necessary and redundant as only the factory can call the `changeController` function inside the `Vault` contract.

#### Impact - NON CRITICAL

#### Proof of Concept

Instances occurs in :

File: src/VaultFactory.sol

[function changeController(uint256 _marketIndex, address _controller) public onlyAdmin](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L345-L359)

File: src/rewards/StakingRewards.sol

[function changeController(address _controller) public onlyFactory](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L295-L299)

As you can see both `changeController` functions checks for the non-zero `_controller` address  :

```
File: src/VaultFactory.sol

    function changeController(uint256 _marketIndex, address _controller)
        public
        onlyAdmin
    {
        if(_controller == address(0))
            revert AddressZero();

        address[] memory vaults = indexVaults[_marketIndex];
        Vault insr = Vault(vaults[0]);
        Vault risk = Vault(vaults[1]);
        insr.changeController(_controller);
        risk.changeController(_controller);

        emit changedController(_marketIndex, _controller);
    }

File: src/Vault.sol

    function changeController(address _controller) public onlyFactory {
        if(_controller == address(0))
            revert AddressZero();
        controller = _controller;
    }
```

#### Mitigation

Remove one of those checks and leave only the non-zero `_controller` address checks in the `changeController` function from the `VaultFactory`.

### 5- Misorganized checks in the `createNewMarket` function inside the `VaultFactory` contract :

In the `createNewMarket` function there is first a check that calls the controller contract to get the vault factory and then a check to verify that the controller is set (controller != address(0)), which doesn't make sense because if the controller is not set the first check will automaticaly revert as `getVaultFactory()` will return address(0) thus the second check is unecessary.

#### Impact - NON CRITICAL

#### Proof of Concept

Instances occurs in :

```
File: src/VaultFactory.sol

    if(
        IController(controller).getVaultFactory() != address(this)
        )
        revert AddressFactoryNotInController();

    if(controller == address(0))
        revert ControllerNotSet();
```

#### Mitigation

To fix this issue there is two solutions :

* Remove completely the `controller == address(0)` check as it's redundant in this case.

* Inverse the two checks so that the `createNewMarket` function checks first if the controller is set and then calls the controller contract, in this case if the controller is not set it will avoid an unecessary external call.

### 6- Named return variables not used anywhere in the function :

When Named return variable are declared they should be used inside the function instead of the return statement or if not they should be removed to avoid confusion.

#### Impact - NON CRITICAL

#### Proof of Concept
Instances include:

File: src/VaultFactory.sol

[returns (address insr, address rsk)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L186)

[returns (address[] memory vaults)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L388)

File: src/Vault.sol

[returns (uint256 shares)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L162)

[returns (uint256 shares)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L185)

[returns (uint256 shares)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L213)

[returns (uint256 feeValue)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L263)

[returns (uint256 entitledAmount)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L381)

[returns (uint256 nextEpochEnd)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L441)

File: src/Controller.sol

[returns (int256 nowPrice)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L264)

File: src/rewards/RewardsFactory.sol

[returns (address insr, address risk)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L86)

[returns (bytes32 hashedIndex)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L148)

#### Mitigation

Either use the named return variables inplace of the return statement or remove them.

### 7- `public` functions not called by the contrat should be declared `external` instead  :

#### Impact - NON CRITICAL

#### Proof of Concept
Instances include:

File: src/VaultFactory.sol

[function createNewMarket(uint256 _withdrawalFee, address _token, int256 _strikePrice, uint256 epochBegin, uint256 epochEnd, address _oracle, string memory _name) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L178-L186)

[function deployMoreAssets(uint256 index, uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L248-L253)

[function setController(address _controller) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L295)

[function changeTreasury(address _treasury, uint256 _marketIndex) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L308-L310)

[function changeTimewindow(uint256 _marketIndex, uint256 _timewindow) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L327-L329)

[function changeController(uint256 _marketIndex, address _controller) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L345-L347)

[function changeOracle(address _token, address _oracle) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L366)

[function getVaults(uint256 index) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L385-L388)

File: src/Vault.sol

[function deposit(uint256 id, uint256 assets, address receiver) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L152-L162)

[function changeTreasury(address _treasury) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L277)

[function changeTimewindow(uint256 _timewindow) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L287)

[function changeController(address _controller) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L295)

[function createAssets(uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L307-L309)

[function endEpoch(uint256 id, bool depeg) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L336-L339)

[function setClaimTVL(uint256 id, uint256 claimTVL) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L350)

[function sendTokens(uint256 id, address _counterparty) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L360-L363)

File: src/Controller.sol

[function triggerDepeg(uint256 marketIndex, uint256 epochEnd) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L148-L150)

[function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L198)

#### Mitigation

Declare the functions aforementioned external.

### 8- Use scientific notation for better readability :

For readability, it is best to use scientific notation (e.g `10e5`) rather than decimal literals(`100000`) or exponentiation(`10**5`)

#### Impact - NON CRITICAL

#### Proof of Concept
Instances include:

File: src/oracles/PegOracle.sol

[nowPrice = (price2 * 10000) / price1;](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L68)

[nowPrice = (price1 * 10000) / price2;](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L70)

[nowPrice / 1000000](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L78)

File: src/Vault.sol

[return (amount * epochFee[_epoch]) / 1000;](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L266)

#### Mitigation
Replace the numbers aforementioned with their scientific notation

### 9- `constant` should be defined rather than using magic numbers :

It is best practice to use constant variables rather than hex/numeric literal values to make the code easier to understand and maintain. 

#### Impact - NON CRITICAL

#### Proof of Concept
Instances include:

File: src/Vault.sol

[if(_withdrawalFee > 150)](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L311)

[keccak256(abi.encodePacked("rY2K"))](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L389)

#### Mitigation
Replace the hex/numeric literals aforementioned with `constants`