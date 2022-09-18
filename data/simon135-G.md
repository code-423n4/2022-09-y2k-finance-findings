## undo  initilzation of state variable  to save 20,000 gas 
```
    uint256 public periodFinish = 0;
```
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L32
##  make requrie with uint > into != to save gas 
https://github.com/code-423n4/2022-09-y2k-finance/blob/bca5080635370424a9fe21fe1aded98345d1f723/src/rewards/StakingRewards.sol#L115
## make require into error to save gas 
```
SemiFungibleVault.sol:89:        require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
SemiFungibleVault.sol:114:        require(
oracles/PegOracle.sol:23:        require(_oracle1 != address(0), "oracle1 cannot be the zero address");
oracles/PegOracle.sol:24:        require(_oracle2 != address(0), "oracle2 cannot be the zero address");
oracles/PegOracle.sol:25:        require(_oracle1 != _oracle2, "Cannot be same Oracle");
oracles/PegOracle.sol:28:        require(
oracles/PegOracle.sol:98:        require(price1 > 0, "Chainlink price <= 0");
oracles/PegOracle.sol:99:        require(
oracles/PegOracle.sol:103:        require(timeStamp1 != 0, "Timestamp == 0 !");
oracles/PegOracle.sol:121:        require(price2 > 0, "Chainlink price <= 0");
oracles/PegOracle.sol:122:        require(
oracles/PegOracle.sol:126:        require(timeStamp2 != 0, "Timestamp == 0 !");
Vault.sol:156:        require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
Vault.sol:178:        require(msg.value > 0, "ZeroValue");
Controller.sol:143:        //require this function cannot be called twice in the same epoch for the same vault
Controller.sol:198:        //require this function cannot be called twice in the same epoch for the same vault
rewards/StakingRewards.sol:92:        require(amount != 0, "Cannot stake 0");
rewards/StakingRewards.sol:115:        require(amount > 0, "Cannot withdraw 0");
rewards/StakingRewards.sol:198:        require(
rewards/StakingRewards.sol:213:        require(
rewards/StakingRewards.sol:222:        require(
rewards/RewardsDistributionRecipient.sol:11:        require(
```
## make adress(0) into long form to save gas ex:0x000000
```
VaultFactory.sol:119:    mapping(uint256 => address[]) public indexVaults; //[0] hedge and [1] risk vault
VaultFactory.sol:149:        if(_admin == address(0))
VaultFactory.sol:151:        if(_weth == address(0))
VaultFactory.sol:154:        if(_treasury == address(0))
VaultFactory.sol:192:        if(controller == address(0))
VaultFactory.sol:221:        if (tokenToOracle[_token] == address(0)) {
VaultFactory.sol:254:        if(controller == address(0))
VaultFactory.sol:260:        address hedge = indexVaults[index][0];
VaultFactory.sol:296:        if(_controller == address(0))
VaultFactory.sol:349:        if(_controller == address(0))
VaultFactory.sol:367:        if(_oracle == address(0))
VaultFactory.sol:369:        if(_token == address(0))

```
##  make i++ into ++i to save gas 
```
Vault.sol:429:        for (uint256 i = 0; i < epochsLength(); i++) {
```
## make onlyOnwer functions payable to save gas 
```
rewards/StakingRewards.sol:211:        onlyOwner
rewards/StakingRewards.sol:221:    function setRewardsDuration(uint256 _rewardsDuration) external onlyOwner {
VaultFactory.sol:129:    modifier onlyAdmin() {
VaultFactory.sol:186:    ) public onlyAdmin returns (address insr, address rsk) {
VaultFactory.sol:253:    ) public onlyAdmin {
VaultFactory.sol:295:    function setController(address _controller) public onlyAdmin {
VaultFactory.sol:310:        onlyAdmin
VaultFactory.sol:329:        onlyAdmin
VaultFactory.sol:347:        onlyAdmin
VaultFactory.sol:366:    function changeOracle(address _token, address _oracle) public onlyAdmin {
Controller.sol:73:    modifier onlyAdmin() {
rewards/RewardsFactory.sol:52:    modifier onlyAdmin() {
rewards/RewardsFactory.sol:85:        onlyAdmin

```
