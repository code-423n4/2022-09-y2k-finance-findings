# `createNewMarket` have inverted condition check
### Target codebase

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L187-L193

# **Vulnerability details**

The function `createNewMarket` incorrect revert if input with zero address because of inverted order of `if` conditions.

## **Recommended Mitigation Steps**

Is recommended to follow the code snippet:

```diff
function createNewMarket(...) ... {
+ if(controller == address(0))
+   revert ControllerNotSet();

	if(
    IController(controller).getVaultFactory() != address(this)
  )  revert AddressFactoryNotInController();

- if(controller == address(0))
-    revert ControllerNotSet();

  ...
}
```

---

# Event not used

### Target codebase

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L97

# **Vulnerability details**

The event is declared but not used.

## **Recommended Mitigation Steps**

Is recommended to either add emit to event in correct function or remove it.

---

# Return is not necessary

### Target codebase

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L425

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L76-#82

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L105

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L128

# **Vulnerability details**

Some functions return already return named variables, this is not needed

## **Recommended Mitigation Steps**

Is recommended to follow the code snippet:

```solidity
//Function with already named return: amount	
function randomFnc(uint256 x) external returns(uint256 amount){
	amount = x * 2;
	//Is not necessary to return amount
	//return amount;
}

//In PegOracle.sol
- nowPrice = nowPrice * decimals10;
+ nowPrice = (nowPrice * decimals10) / 1000000;

- return (
-    roundID1,
-    nowPrice / 1000000,
-    startedAt1,
-    timeStamp1,
-    answeredInRound1
- );
```

---

# Lack of zero address check

### Target codebase

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L308

# **Vulnerability details**

Some bugs happens because of invalid inputs, it's a good pattern to check if input values are valid or inside a range

## **Recommended Mitigation Steps**

Is recommended to follow this changes:

```diff
function changeTreasury(address _treasury, uint256 _marketIndex)
    public
    onlyAdmin
{
+	if(_treasury == address(0))
+    revert AddressZero();

  treasury = _treasury;
	...
}
```