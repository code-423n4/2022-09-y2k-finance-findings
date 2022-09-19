L-01 SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements IERC721Receiver

https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L169

L-02 REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert() do
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190
N-1  Event is missing indexed fields
Each event should use three indexed fields if there are three or more fields
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L49
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L35
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L51

N-2 Unused named returns
Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity:
https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L46