* Contract ```StakingRewards``` does not need ```SafeMath``` when using the Solidity version starting from 0.8:
```solidity
  pragma solidity 0.8.15;
  using SafeMath for uint256;
  _balances[msg.sender] = _balances[msg.sender].add(amount);
```

* When depositing ETH to the vault, users will need an upfront approval of WETH because it wraps the native asset to WETH ERC20 and then calls ```deposit``` which respectively calls ```transferFrom```:
```solidity
  function depositETH(uint256 id, address receiver)
        ...
        IWETH(address(asset)).deposit{value: msg.value}();
        assert(IWETH(address(asset)).transfer(msg.sender, msg.value));
        return deposit(id, msg.value, receiver);
    }
    
  function deposit(
        uint256 id,
        uint256 assets,
        address receiver
    )
        ...
        asset.transferFrom(msg.sender, address(this), shares);
```
It would be more convenient to adapt these functions so that users won't need extra approval txs.

* In ```SemiFungibleVault``` / ```Vault``` when ```isApprovedForAll(owner, receiver)``` is true then anyone can withdraw from the vault on behalf of the owner:
```solidity
    function withdraw(
        uint256 id,
        uint256 assets,
        address receiver,
        address owner
    ) external virtual returns (uint256 shares) {
        require(
            msg.sender == owner || isApprovedForAll(owner, receiver),
            "Only owner can withdraw, or owner has approved receiver for all"
        );
        ...
```
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
    {
        if(
            msg.sender != owner &&
            isApprovedForAll(owner, receiver) == false)
            revert OwnerDidNotAuthorize(msg.sender, owner);
```
I believe that assets should only be withdrawn by the intention of either the owner or the receiver, not by anyone.

* Misleading comment:
```solidity
    /** @notice You can only call functions that use this modifier after the current epoch has started
      */
    modifier epochHasEnded(uint256 id)
```