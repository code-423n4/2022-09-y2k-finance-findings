### [LOW-1] Failing test ``testOwnerAuthorize()`` from ``AssertTest.t.sol`` file

is failing because is not enough to vm.warp but we still need to call a controller function before withdrawing. Signaling depegging or ending of an epoch.

After the line ``vm.warp(endEpoch + 1 days);`` we can call ``controller.triggerEndEpoch(SINGLE_MARKET_INDEX, endEpoch);``.  

More correctly we should call the function that signals depegging because we are simulating a depeg situation with the FakeOracle.

In this case before the line ``vm.warp(endEpoch + 1);`` we can call two functions like this :

```
vm.warp(endEpoch - 100);
controller.triggerDepeg(SINGLE_MARKET_INDEX, endEpoch);
```

In both situations now the test passes because withdrawal is succesfully executed.

### [LOW-02] Failing test ``testFuzzDeposit`` from ``FuzzTest.t.sol`` file

is failing because 2 small mistakes :

1) Taken from foundry book https://book.getfoundry.sh/forge/fuzz-testing :

The default amount of ether that the test contract is given is 2**96 wei (as in DappTools), so we have to restrict the type of amount to uint96 to make sure we don't try to send more than we have to change the function parameter signature from ``uint256 ethValue`` to ``uint96 ethValue``

2) We need to tell the fuzzer not to test 0 value by adding the line vm.assume like this:

```
function testFuzzDeposit(uint96 ethValue) public {
        vm.assume(ethValue > 0);
        ...
```


### [LOW-03] Failing test ``testFuzzWithdraw()`` from ``FuzzTest.t.sol`` file

Same mistake as in ``testFuzzDeposit``. 

We need to change from:

```
function testFuzzWithdraw(uint256 ethValue) public {
```

to

```
function testFuzzWithdraw(uint96 ethValue) public {
```

### [LOW-04] Wrong comment for Oracle parameter type
In line :
 https://github.com/code-423n4/2022-09-y2k-finance/blob/2175c044af98509261e4147edeb48e1036773771/src/VaultFactory.sol#L171 the true type for strikePrice parameter is not uint256 but int256 because ``AggregatorV3Interface`` is using int256 type for the nowPrice.


### [LOW-05] Consider rename modifier ``epochHasEnded``

Insted of ``epochHasEnded`` mybe call it for what it trully does ``epochTimeStampHasEnded`` maybe. 

Considering the implementation:

```
modifier epochHasEnded(uint256 id) {
        if((block.timestamp < id) && idDepegged[id] == false)
            revert EpochNotFinished();
        _;
    }
```
The function is really checking the epoch window timestamp is ended plus if we are not in a depegged situation.

I found this problematic when ``testOwnerAuthorize()`` was failing because the call to witdraw (which is using this modifier). In my opinion it should revert with ``EpochNotFinshed`` because the kepper/controller did not call ``triggerEndEpoch`` (you see here End Epoch) or triggerDepeg. Instead it reverts without this reason and failing the test.

The main problem is that we should have a signal that we are missing a call for "ending" an epoch.

### [LOW-06] ``Vault::getNextEpoch`` can fail

This function is not tested. It doesn`t seem to be used anywhere in codebase.

If the Vault has many epochs the for loop can consume all the transaction gas.

If ever needed a possible mitigation is to add next/previous mapping in relation to the current epoch instead of looping.

### [LOW-07] Do not need ``SafeMath`` in ``StakingReward`` contract

Because you are using solidity version 8 you do not need to use SafeMath library.