1 :
++I COSTS LESS GAS COMPARED TO I++ OR I += 1 IN FOR LOOPS (~5 GAS PER ITERATION)
++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i.++i returns the actual incremented value with i++ the compiler has to create a temporary variable (when used) for returning 1 instead of 2.Ex:
Vault.sol#L443
        for (uint256 i = 0; i < epochsLength(); i++) {
            if (epochs[i] == _epoch) {
                if (i == epochsLength() - 1) {
                    return 0;
                }
                return epochs[i + 1];

2:

