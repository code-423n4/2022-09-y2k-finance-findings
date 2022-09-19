## **++i costs less gas compared to i++ or i += 1**

### Target codebase

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L195

# **Vulnerability details**

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integers. This is because the pre-increment operation is cheaper (about 5 GAS per iteration).

## **Recommended Mitigation Steps**

Is recommended to follow this code snippet:

```diff
- marketIndex += 1;
+ ++marketIndex;
```

---

# External function consumes less gas than public

### Target codebase

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L438

# **Vulnerability details**

Functions with the `public` visibility modifier cost more gas than `external`

## **Recommended Mitigation Steps**

Is recommended to use `external` instead of `public` in functions that will be only used externally, example:

```solidity
contract Test {

    string message = "Hello World";

    // Execution cost: 24527
    function test() public view returns (string memory){
         return message;
    }

    //Execution cost: 24505
    function test2() external view returns  (string memory){
         return message;
    }
}
```

---

# Optimize loops

### Target codebase

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443

# **Vulnerability details**

The loops aren't optimized.

## **Recommended Mitigation Steps**

1. Default values don't need to be declared
    
    ```diff
    - uint256 i = 0;
    - bool flag = false;
    
    + uint256 i;
    + bool flag;
    ```
    
2. Cache array length
    
    ```diff
    - for (...; i < epochsLength(); ...) {
    
    + uint256 eLength = epochsLength();
    + for (...; i < eLength; ...) {
    ```
    
3. Uncheck loop and use ++i
    
    ```diff
    - for (...; ...; i++) {
    + for (...; ...;) {
    	...
    + unchecked{ ++i; }
    }
    ```
    

Follow this code to save gas:

```diff
- for (uint256 i = 0; i < epochsLength(); i++) {

+ uint256 eLength = epochsLength();
+ for (uint256 i; i < eLength;) {
	...
+ unchecked{ ++i; }
}
```

---

# Default values don't need to be declared

### Target codebase

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L36

# **Vulnerability details**

Declaring default values for variables cost more gas.

## **Recommended Mitigation Steps**

Follow this code snippet:

```diff
- uint256 i = 0;
- bool flag = false;

+ uint256 i;
+ bool flag;
```