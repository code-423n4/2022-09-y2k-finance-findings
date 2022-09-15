## Unused onlyAdmin modifier in Controller.sol

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L73

```
    modifier onlyAdmin() {
        if(msg.sender != admin)
            revert AddressNotAdmin();
        _;
    }
```

is not used. Please remove

## Use require instead of assert in Vault.sol when handling WETH transfer

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L190

```
   IWETH(address(asset)).deposit{value: msg.value}();
   assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/utils/SafeERC20.sol

We recommand the project use SafeERC20 and use safeTransfer, 

or replace assert with require.

## Unhandled external return value when calling transfer and transferFrom in Vault.sol

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L167

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L228

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L231

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L365

when calling transfer or transferFrom in Vault.sol

```
      asset.transfer(_counterparty, idFinalTVL[id]);
```

```
    asset.transferFrom(msg.sender, address(this), shares);
```

the return value is not handled.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/utils/SafeERC20.sol

We recommand the project use SafeERC20 and use safeTransfer, 

## Increase test coverage and make sure the test pass

test fails when running test.

by running

```
 forge test
```

the result is 

```
Encountered a total of 20 failing tests, 32 tests succeeded
```

by running 

```
 forge test --match-contract AssertTest --fork-url https://arb1.arbitrum.io/rpc -vv
```

the result is 

```
Encountered a total of 1 failing tests, 16 tests succeeded
```

the test fails.

Please make sure proper test is implemented to increase the test coverage.

## Use transfer instead of transferFrom in StakingRewards.sol

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L122

instead of 

```
  stakingToken.safeTransferFrom(
            address(this),
            msg.sender,
            id,
            amount,
            ""
    );
```

we can write

```
  stakingToken.safeTransfer(
            msg.sender,
            id,
            amount,
            ""
    );
```

## Outdated Openzeppelin version 

the openzepplein version used is "version": "4.6.0",

however, the most updated openzeppelin version is 4.7.3

there are known vulnerabliilty in outdated version including version 4.6.0.

https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts

please use the up to date code version

## getNextEpoch in Vault.sol is not used and has potential index out of range error

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L438

```
    function getNextEpoch(uint256 _epoch)
        public
        view
        returns (uint256 nextEpochEnd)
    {
        for (uint256 i = 0; i < epochsLength(); i++) {
            if (epochs[i] == _epoch) {
                if (i == epochsLength() - 1) {
                    return 0;
                }
                return epochs[i + 1];
            }
        }
    }
```

the function above is not used anywhere.

the line 

```
 return epochs[i + 1]; 
```
can throw index out of range error.

## Redundant safeMath usage in StakingReward.sol

the solidtity version above 0.8.0 handles overflow and underflow in arithmitic operation so no need to use safe math.

## Confusing name in Vault.sol in deposit function

the variable assets, according to the comment

```
      @param  assets  uint256 representing how many assets the user wants to deposit, a fee will be taken from this value;
```

in the function,

```
    function deposit(
        uint256 id,
        uint256 assets,
        address receiver
    )
```

however the name is confusing because assets make people think it is a list of token asset. We recommand the project change the assets name to assetsAmount.

## missing zero address check in Vault.sol in deposit and withdraw function for receiver

we recommand the project check if the receiver of the shares is address(0)

## missing Event emission when changing crucial smart contract state in Vault.sol

the function below does not emit event when changing critical state.

```
    function changeTreasury(address _treasury) public onlyFactory {
```

```
    function changeTimewindow(uint256 _timewindow) public onlyFactory {
```

```
       function changeController(address _controller) public onlyFactory {
```

```
      function createAssets(uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee)
```