### [L-01] ```require()``` should be used instead of ```assert()```


#### Impact
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as ```require()```/```revert()``` do. ```assert()``` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.


#### Findings:
```
src/Vault.sol:L190        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));

```
### [L-02] ```decimals()``` not part of ERC20 standard.


#### Impact
```decimals()``` is not part of the official ERC20 standard and might fall for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.


#### Findings:
```
src/Controller.sol:L299        uint256 decimals = 10**(18-(priceFeed.decimals()));

src/oracles/PegOracle.sol:L29            (priceFeed1.decimals() == priceFeed2.decimals()),

src/oracles/PegOracle.sol:L36        decimals = priceFeed1.decimals();

src/oracles/PegOracle.sol:L73        int256 decimals10 = int256(10**(18 - priceFeed1.decimals()));

```
### [L-03] ```_safemint()``` should be used rather than ```_mint()``` wherever possible


#### Impact
```_mint()``` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of ```_safeMint()``` which ensures that the recipient is either an EOA or implements ```IERC721Receiver```. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function


#### Findings:
```
src/SemiFungibleVault.sol:L96        _mint(receiver, id, shares, EMPTY);

src/Vault.sol:L169        _mint(receiver, id, shares, EMPTY);

```
### [L-04] Events not emitted for important state changes.


#### Impact
When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.


#### Findings:
```
src/Vault.sol:L350             function setClaimTVL(uint256 id, uint256 claimTVL) public onlyController {

```
### [N-01] Open TODOs


#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.


#### Findings:
```
src/Vault.sol:L196    @notice Withdraw entitled deposited assets, checking if a depeg event //TODO add GOV token rewards

```
