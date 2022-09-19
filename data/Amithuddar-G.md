1)++i costs less gas than i++, especially when it’s used in for-loops (--i/i-- too)

Saves 6 gas PER LOOP

File: 2022-09-y2k-finance\src\Vault.sol
  443,49:         for (uint256 i = 0; i < epochsLength(); i++) {
  
2) ++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow,
as is the case when used in for- and while-loops    

File: 2022-09-y2k-finance\src\Vault.sol
  443,49:         for (uint256 i = 0; i < epochsLength(); i++) {


4)It costs more gas to initialize variables to zero than to let the default of zero be applied

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {  
    
File: 2022-09-y2k-finance\src\Vault.sol
  443,49:         for (uint256 i = 0; i < epochsLength(); i++) {

6)X = X + Y IS CHEAPER THAN X += Y 

File: 2022-09-y2k-finance\src\VaultFactory.sol
  195,21:         marketIndex += 1;	
  
5)Use custom errors rather than revert()/require() strings to save gas

File: 2022-09-y2k-finance\src\SemiFungibleVault.sol
  91,9:         require((shares = previewDeposit(id, assets)) != 0, "ZERO_SHARES");
  116,9:         require(  
                        msg.sender == owner || isApprovedForAll(owner, receiver),
                        "Only owner can withdraw, or owner has approved receiver for all"
                       ); 
					   
File: 2022-09-y2k-finance\src\Vault.sol
  165,9:         require((shares = previewDeposit(id, assets)) != 0, "ZeroValue");
  187,9:         require(msg.value > 0, "ZeroValue");

File: 2022-09-y2k-finance\src\oracles\PegOracle.sol
  23,9:         require(_oracle1 != address(0), "oracle1 cannot be the zero address");
  24,9:         require(_oracle2 != address(0), "oracle2 cannot be the zero address");
  25,9:         require(_oracle1 != _oracle2, "Cannot be same Oracle");
  28,9:         require(
                          (priceFeed1.decimals() == priceFeed2.decimals()),
                          "Decimals must be the same"
                      );
  98,9:         require(price1 > 0, "Chainlink price <= 0");
  99,9:         require(
                          answeredInRound1 >= roundID1,
                        "RoundID from Oracle is outdated!"
                         );
  103,9:         require(timeStamp1 != 0, "Timestamp == 0 !");
  121,9:         require(price2 > 0, "Chainlink price <= 0");
  122,9:          require(
            answeredInRound2 >= roundID2,
            "RoundID from Oracle is outdated!"
        );
  126,9:         require(timeStamp2 != 0, "Timestamp == 0 !");
  
File: 2022-09-y2k-finance\src\rewards\StakingRewards.sol
  96,9:         require(amount != 0, "Cannot stake 0");
  119,9:         require(amount > 0, "Cannot withdraw 0");
  202,9:         require(
            tokenAddress != address(stakingToken),
            "Cannot withdraw the staking token"
        );
  217,9:          require(
            block.timestamp > periodFinish,
            "Previous rewards period must be complete before changing the duration for the new period"
        );
  226,9:           require(
            block.timestamp > periodFinish,
            "Previous rewards period must be complete before changing the duration for the new period"
        );  

8) USING > 0 COSTS MORE GAS THAN != 0 WHEN USED ON A UINT IN A REQUIRE() STATEMENT

This change saves 6 gas per instance

File: 2022-09-y2k-finance\src\Vault.sol
  187,27:         require(msg.value > 0, "ZeroValue");
  
File: 2022-09-y2k-finance\src\oracles\PegOracle.sol
  98,24:         require(price1 > 0, "Chainlink price <= 0");
  121,24:         require(price2 > 0, "Chainlink price <= 0");

File: 2022-09-y2k-finance\src\rewards\StakingRewards.sol
  119,24:         require(amount > 0, "Cannot withdraw 0");
 