- [Gas](#gas)
    - [**1. Reduce the size of error messages Long revert Strings**](#1-reduce-the-size-of-error-messages-long-revert-strings)
        - [Total gas saved: **18 * 7 = 126**](#total-gas-saved-18--7--126)
    - [**2. Use Custom Errors instead of Revert Strings to save Gas**](#2-use-custom-errors-instead-of-revert-strings-to-save-gas)
        - [Total gas saved: **9 * 19 = 171**](#total-gas-saved-9--19--171)
    - [**3. Gas saving using immutable**](#3-gas-saving-using-immutable)
    - [**4. Remove unnecessary variables**](#4-remove-unnecessary-variables)
    - [**5. Reduce boolean comparison**](#5-reduce-boolean-comparison)
        - [Total gas saved: **15 * 5 = 75**](#total-gas-saved-15--5--75)
        - [Total gas saved: **18 * 1 = 18**](#total-gas-saved-18--1--18)
        - [Total gas saved: **18 * 1 = 18**](#total-gas-saved-18--1--18)
    - [**6. Local variable not needed for conditional**](#6-local-variable-not-needed-for-conditional)
    - [**7. Avoid compound assignment operator in state variables**](#7-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 1 = 13**](#total-gas-saved-13--1--13)
    - [**8. ++i costs less gas compared to i++ or i += 1**](#8-i-costs-less-gas-compared-to-i-or-i--1)
    - [**9. Remove empty blocks**](#9-remove-empty-blocks)
    - [**10. There's no need to set default values for variables**](#10-theres-no-need-to-set-default-values-for-variables)
        - [Total gas saved: **8 * 3 = 24**](#total-gas-saved-8--3--24)
    - [**11. Don't use the method's output for loops condition**](#11-dont-use-the-methods-output-for-loops-condition)
    - [**12. Use require instead of assert**](#12-use-require-instead-of-assert)
    - [**13. Use immutable variable for optimize**](#13-use-immutable-variable-for-optimize)

# Gas

## **1. Reduce the size of error messages (Long revert Strings)**

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testShortRevert(bool path) public view {
require(path, "test error");
}
}

contract TesterB {
function testLongRevert(bool path) public view {
require(path, "test big error message, more than 32 bytes");
}
}
```

Gas saving executing: **18 per entry**

```
TesterA.testShortRevert: 21886   
TesterB.testLongRevert:  21904       
```

**Affected source code:**

- [SemiFungibleVault.sol:116-119](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L116-L119)
- [PegOracle.sol:23](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L23)
- [PegOracle.sol:24](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L24)
- [PegOracle.sol:99-102](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L99-L102)
- [PegOracle.sol:122-125](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L122-L125)
- [StakingRewards.sol:217-220](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L217-L220)
- [StakingRewards.sol:226-229](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L226-L229)

### Total gas saved: **18 * 7 = 126**

## **2. Use Custom Errors instead of Revert Strings to save Gas**

Custom errors from Solidity `0.8.4` are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

**Source Custom Errors in Solidity:**

Starting from Solidity `v0.8.4`, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testRevert(bool path) public view {
 require(path, "test error");
}
}

contract TesterB {
error MyError(string msg);
function testError(bool path) public view {
 if(path) revert MyError("test error");
}
}
```

Gas saving executing: **9 per entry**

```
TesterA.testRevert: 21611
TesterB.testError:  21602     
```

**Affected source code:**

- [SemiFungibleVault.sol:91](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L91)
- [SemiFungibleVault.sol:116-119](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L116-L119)
- [Vault.sol:165](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L165)
- [Vault.sol:187](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L187)
- [PegOracle.sol:23](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L23)
- [PegOracle.sol:24](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L24)
- [PegOracle.sol:25](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L25)
- [PegOracle.sol:28-31](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L28-L31)
- [PegOracle.sol:98](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L98)
- [PegOracle.sol:99-102](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L99-L102)
- [PegOracle.sol:103](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L103)
- [PegOracle.sol:121](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L121)
- [PegOracle.sol:122-125](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L122-L125)
- [PegOracle.sol:126](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L126)
- [StakingRewards.sol:96](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L96)
- [StakingRewards.sol:119](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L119)
- [StakingRewards.sol:202-205](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L202-L205)
- [StakingRewards.sol:217-220](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L217-L220)
- [StakingRewards.sol:226-229](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L226-L229)

### Total gas saved: **9 * 19 = 171**

## **3. Gas saving using `immutable`**

It's possible to avoid storage access a save gas using `immutable` keyword for the following variables:

It's also better to remove the initial values, because they will be set during the constructor.

**Affected source code:**

- [PegOracle.sol:13](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L13)
- [PegOracle.sol:15](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L15)
- [PegOracle.sol:16](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L16)

## **4. Remove unnecessary variables**

The following state variables can be removed without affecting the logic of the contract since they are not used and/or are redundant.

**Affected source code:**

- [PegOracle.sol:10](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L10)
- [PegOracle.sol:11](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L11)
    
## **5. Reduce boolean comparison**

Compare a boolean value using `== true` or `== false` instead of using the boolean value is more expensive.

`NOT` opcode it's cheaper than using `EQUAL` or `NOTEQUAL` when the value it's false, or just the value without `== true` when it's true, because it will use less opcodes inside the VM.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testEqual(bool a) public view returns (bool) { return a == true; }
}

contract TesterB {
function testNot(bool a) public view returns (bool) { return a; }
}
```

Gas saving executing: **18 per entry for == true**

```
TesterA.testEqual:   21814
TesterB.testNot:     21796   
```

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testEqual(bool a) public view returns (bool) { return a == false; }
}

contract TesterB {
function testNot(bool a) public view returns (bool) { return !a; }
}
```

Gas saving executing: **15 per entry for == false**

```
TesterA.testEqual:   21814
TesterB.testNot:     21799
```

**Affected source code:**

Use the value instead of `== false`:

- [RewardsFactory.sol:96](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L96)
- [Controller.sol:93](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L93)
- [Controller.sol:211](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L211)
- [Vault.sol:96](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L96)
- [Vault.sol:216-217](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L216-L217)

### Total gas saved: **15 * 5 = 75**

Use the value instead of `!= true`:

- [Vault.sol:80](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L80)

### Total gas saved: **18 * 1 = 18**

Use the value instead of `== true`:

- [Vault.sol:314](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L314)

### Total gas saved: **18 * 1 = 18**

## **6. Local variable not needed for conditional**

The following local variables are not necessary and can be avoided by directly using the variable value in the conditional, this will avoid creating variables on the stack and reduce computational cost.

Remove `bool isSequencerUp`:
- [Controller.sol:277](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L277)

Remove `uint256 timeSinceUp`:
- [Controller.sol:283](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L283)

## **7. Avoid compound assignment operator in state variables**

Using compound assignment operators for state variables (like `State += X` or `State -= X` ...) it's more expensive than using operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint private _a;
function testShort() public {
_a += 1;
}
}

contract TesterB {
uint private _a;
function testLong() public {
_a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [VaultFactory.sol:195](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L195)

### Total gas saved: **13 * 1 = 13**

## **8. `++i` costs less gas compared to `i++` or `i += 1`**

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integers, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:

```solidity
uint i = 1;
i++; // == 1 but i == 2
```

But `++i` returns the actual incremented value:

```solidity
uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable
```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`
I suggest using `++i` instead of `i++` to increment the value of an uint variable. Same thing for `--i` and `i--`

*Keep in mind that this change can only be made when we are not interested in the value returned by the operation, since the result is different, you only have to apply it when you only want to increase a counter.*

**Affected source code:**

- [Vault.sol:443](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L443)

## **9. Remove empty blocks**

An empty block or an empty method generally indicates a lack of logic that it’s unnecessary and should be eliminated, call a method that literally does nothing only consumes gas unnecessarily, if it is a `virtual` method which is expects it to be filled by the class that implements it, it is better to use `abstract`  contracts with just the definition.

**Sample code:**

```javascript
pragma solidity >=0.4.0 <0.7.0;

abstract contract Feline {
    function utterance() public virtual returns (bytes32);
}
```

**Reference:**
- https://docs.soliditylang.org/en/v0.6.0/contracts.html?highlight=abstract#abstract-contracts

**Affected source code:**

- [SemiFungibleVault.sol:276-280](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L276-L280)
- [SemiFungibleVault.sol:282-286](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L282-L286)

## **10. There's no need to set default values for variables**

If a variable is not set/initialized, the default value is assumed (0, `false`, 0x0 ... depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testInit() public view returns (uint) { uint a = 0; return a; }
}

contract TesterB {
function testNoInit() public view returns (uint) { uint a; return a; }
}
```

Gas saving executing: **8 per entry**

```
TesterA.testInit:   21392
TesterB.testNoInit: 21384
```

**Affected source code:**

- [VaultFactory.sol:159](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L159)
- [StakingRewards.sol:36](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/StakingRewards.sol#L36)
- [Vault.sol:443](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L443)

### Total gas saved: **8 * 3 = 24**

## **11. Don't use the method's output for loops condition**

It's cheaper to store the dynamic value inside a local variable and iterate over it.

**Affected source code:**

- [Vault.sol:443](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L443)

## **12. Use `require` instead of `assert`**

The `assert()` and `require()` functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the `assert()` function when false, **uses up all the remaining gas and reverts all the changes made.**

Meanwhile, a `require()` function when false, also reverts back all the changes made to the contract but **does refund all the remaining gas fees we offered to pay**. This is the most common Solidity function used by developers for debugging and error handling.

**Affected source code:**

- [Vault.sol:190](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L190)

## **13. Use immutable variable for optimize**

In the constructor, store a new immutable variable `isrY2K` if the `symbol` is `rY2K`, this will avoid expensive operations like the following:

```diff
    function beforeWithdraw(uint256 id, uint256 amount)
        public
        view
        returns (uint256 entitledAmount)
    {
        // in case the risk wins aka no depeg event
        // risk users can withdraw the hedge (that is paid by the hedge buyers) and risk; withdraw = (risk + hedge)
        // hedge pay for each hedge seller = ( risk / tvl before the hedge payouts ) * tvl in hedge pool
        // in case there is a depeg event, the risk users can only withdraw the hedge
        if (
-           keccak256(abi.encodePacked(symbol)) ==
-           keccak256(abi.encodePacked("rY2K"))
+           isrY2K
        ) {
            if (!idDepegged[id]) {
                //depeg event did not happen
                /*
                entitledAmount =
                    (amount / idFinalTVL[id]) *
                    idClaimTVL[id] +
                    amount;
                */
                entitledAmount =
                    amount.divWadDown(idFinalTVL[id]).mulDivDown(
                        idClaimTVL[id],
                        1 ether
                    ) +
                    amount;
            } else {
                //depeg event did happen
                entitledAmount = amount.divWadDown(idFinalTVL[id]).mulDivDown(
                    idClaimTVL[id],
                    1 ether
                );
            }
        }
```

**Affected source code:**

- [Vault.sol:388-389](https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L388-L389)
