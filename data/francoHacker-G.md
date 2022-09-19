You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

 for (uint256 i = 0; i < epochsLength(); /** NOTE: Removed i++ **/ ) {
                
                unchecked { ++i; }
        }       

instances:

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L443-L450