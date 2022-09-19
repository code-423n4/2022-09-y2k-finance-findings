## **`++I` COSTS LESS GAS THAN `I++`, ESPECIALLY WHEN IT’S USED IN `FOR`-LOOPS (`--I`/`I--` TOO)**

Saves 6 gas *PER LOOP*

*There is 1 instance of this issue:*

```solidity
File: Vault.sol
443:   for (uint256 i = 0; i < epochsLength(); i++) {
```

---

## `<ARRAY>.LENGTH` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A `FOR`LOOP

The overheads outlined below are *PER LOOP*, excluding the first loop

- storage arrays incur a Gwarmaccess (100 gas)
- memory arrays use `MLOAD` (3 gas)
- calldata arrays use `CALLDATALOAD` (3 gas)

Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset.

*There is 1 instance of this issue:*

```solidity
File: Vault.sol
443:   for (uint256 i = 0; i < epochsLength(); i++) {
```

With `epochsLength()` defined as follow :

```solidity
File: Vault.sol
430:    function epochsLength() public view returns (uint256) {
	        return epochs.length;
		    }
```

---

## DON’T USE `SAFEMATH` ONCE THE SOLIDITY VERSION IS 0.8.0 OR GREATER

Version 0.8.0 introduces internal overflow checks, so using `SafeMath` is redundant and adds overhead

*There is 1 instance of this issue:*

```solidity
File: rewards/StakingRewards.sol 
5:   import {SafeTransferLib} from "@solmate/utils/SafeTransferLib.sol";
```

---

## **USING `PRIVATE` RATHER THAN `PUBLIC` FOR CONSTANTS, SAVES GAS**

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

*There is 2 instances of this issue:*

```solidity
File: Controller.sol
16:   uint256 public constant VAULTS_LENGTH = 2;
```

```solidity
File: SemiFungibleVault.sol
22:   bytes internal constant EMPTY = "";
```

---

## ****State variables only set in the constructor should be declared `immutable`**

Avoids a Gsset (**20000 gas**) in the constructor, and replaces each Gwarmacces (**100 gas**) with a `PUSH32`(**3 gas**).

```solidity
File:   oracles/PegOracle.sol
10:     address public oracle1;
11:     address public oracle2;
13:     uint8 public decimals;
```

```solidity
File:   SemiFungibleVault.sol
20:     string public name;
21:     string public symbol;
```

```solidity
File:   rewards/RewardsFactory.sol
9:      address public admin;
10:     address public govToken;
11:     address public factory; 
```

---

## **`PUBLIC` FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED `EXTERNAL` INSTEAD**

Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from `external` to `public`and can save gas by doing so.

*There is 25 instances of this issue:*

```solidity
File:  Controller.sol
148:   function triggerDepeg(uint256 marketIndex, uint256 epochEnd)
	        public
	        isDisaster(marketIndex, epochEnd)
		   {
198:   function triggerEndEpoch(uint256 marketIndex, uint256 epochEnd) public {
```

```solidity
File:  oracles/PegOracle.sol
89:    function getOracle1_Price() public view returns (int256 price) {

```

```solidity
File:  src/SemiFungibleVault.sol
85:    function deposit(
        uint256 id,
        uint256 assets,
        address receiver
    ) public virtual returns (uint256 shares) {
110:   function withdraw(
        uint256 id,
        uint256 assets,
        address receiver,
        address owner
    ) external virtual returns (uint256 shares) {
189:    function previewMint(uint256 id, uint256 shares)
        public
        view
        virtual
        returns (uint256)
    {
221:    function previewRedeem(uint256 id, uint256 shares)
        public
        view
        virtual
        returns (uint256)
    {
237:    function maxDeposit(address) public view virtual returns (uint256) {
244:    function maxMint(address) public view virtual returns (uint256) {
251:    function maxWithdraw(uint256 id, address owner)
        public
        view
        virtual
        returns (uint256)
    {
263:    function maxRedeem(uint256 id, address owner)
        public
        view
        virtual
        returns (uint256)
    {

```

```solidity
File:   Vault.sol
277:    function changeTreasury(address _treasury) public onlyFactory {
287:    function changeTimewindow(uint256 _timewindow) public onlyFactory {
295:    function changeController(address _controller) public onlyFactory {
307:    function createAssets(uint256 epochBegin, uint256 epochEnd, uint256 _withdrawalFee)
        public
        onlyFactory
    {
336:    function endEpoch(uint256 id, bool depeg)
        public
        onlyController
        marketExists(id)
    {
350:    function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {
360:    function sendTokens(uint256 id, address _counterparty)
        public
        onlyController
        marketExists(id)
    {
438:    function getNextEpoch(uint256 _epoch)
        public
        view
        returns (uint256 nextEpochEnd)
    {
```

```solidity
File:  VaultFactory.sol
178:   function createNewMarket(
        uint256 _withdrawalFee,
        address _token,
        int256 _strikePrice,
        uint256 epochBegin,
        uint256 epochEnd,
        address _oracle,
        string memory _name
    ) public onlyAdmin returns (address insr, address rsk) {
248:    function deployMoreAssets(
        uint256 index,
        uint256 epochBegin,
        uint256 epochEnd,
        uint256 _withdrawalFee
    ) public onlyAdmin {
295:    function setController(address _controller) public onlyAdmin {
366:    function changeOracle(address _token, address _oracle) public onlyAdmin {
385:    function getVaults(uint256 index)
        public
        view
        returns (address[] memory vaults)
    {

```

```solidity
File    rewards/RewardsFactory.sol
145:    function getHashedIndex(uint256 _index, uint256 _epoch)
        public
        pure
        returns (bytes32 hashedIndex)
    {
```