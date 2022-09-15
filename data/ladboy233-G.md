
   
## function can be marked as external instead of public


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L2148


```
    function triggerDepeg(uint256 marketIndex, uint256 epochEnd)
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Controller.sol#L2198


```
    function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L285


```
    function deposit(
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L2189


```
    function previewMint(uint256 id, uint256 shares)
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L2221


```
    function previewRedeem(uint256 id, uint256 shares)
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L2237


```
    function maxDeposit(address) public view virtual returns (uint256) {
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L2244


```
    function maxMint(address) public view virtual returns (uint256) {
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L2251


```
    function maxWithdraw(uint256 id, address owner)
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/SemiFungibleVault.sol#L2263


```
    function maxRedeem(uint256 id, address owner)
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L2280


```
    function changeTreasury(address _treasury) public onlyFactory {
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L2290


```
    function changeTimewindow(uint256 _timewindow) public onlyFactory {
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L2298


```
    function changeController(address _controller) public onlyFactory {
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L2310


```
    function createAssets(uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee)
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L2339


```
    function endEpoch(uint256 id, bool depeg)
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L2353


```
    function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L2363


```
    function sendTokens(uint256 id, address _counterparty)
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L2442


```
    function getNextEpoch(uint256 _epoch)
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L2178


```
    function createNewMarket(
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L2248


```
    function deployMoreAssets(
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L2295


```
    function setController(address _controller) public onlyAdmin {
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L2366


```
    function changeOracle(address _token, address _oracle) public onlyAdmin {
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/VaultFactory.sol#L2385


```
    function getVaults(uint256 index)
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L2145


```
    function getHashedIndex(uint256 _index, uint256 _epoch)
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/oracles/PegOracle.sol#L289


```
    function getOracle1_Price() public view returns (int256 price) {
```
            

## ++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT�S USED IN FOR-LOOPS (--I/I-- TOO)
    
Saves 6 gas per loop


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L2447


```
        for (uint256 i = 0; i < epochsLength(); i++) {
```
            

## IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED


https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L2447


```
        for (uint256 i = 0; i < epochsLength(); i++) {
```

## .length can be cached in for loop to save gas

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/Vault.sol#L2447


```
        for (uint256 i = 0; i < epochsLength(); i++) {
```

```
   function epochsLength() public view returns (uint256) {
        return epochs.length;
    }
```
            

## ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
   

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L2118


```
        bytes32 hashedIndex = keccak256(abi.encode(_marketIndex, _epochEnd));
```
            

https://github.com/code-423n4/2022-09-y2k-finance/blob/ac3e86f07bc2f1f51148d2265cc897e8b494adf7/src/rewards/RewardsFactory.sol#L2150


```
        return keccak256(abi.encode(_index, _epoch));
```
            