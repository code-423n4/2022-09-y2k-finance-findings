#Typos

Vault.sol 198

````

    @param assets   uint256 of how many assets you want to withdraw, this value will be used to calculate how many assets you are entitle to according to the events;

````
Missing a 'd' for 'entitled', should be:

````
    @param assets   uint256 of how many assets you want to withdraw, this value will be used to calculate how many assets you are entitled to according to the events;
````

Vault.sol 200:

````

    @param owner    Address of the owner of these said assets;

````

Remove: 'said' (written, not spoken and not specific enough) and change to:

````
    @param owner    Address of the owner of assets to be withdrawn;

````


The error, ZeroValue() exists on line 25, but not implemented - as noted in gas report.


