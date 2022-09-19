### Low Risk Issues List
| Number |Issues Details|Context |
|:--:|:-------|:-------:|
| [L-01] | Missing Event for critical parameters change | 2 |
| [L-02]| Add to `blacklist function` | |
| [L-03] |  _Return value_ of `transfer` not checked | 3 |
| [L-04] | Use `abi.encode()` to avoid collision | 2 |
| [L-05] | Additional steps for price feeds | |
| [L-06] | No check if onERC1155Received is implemented | 1 |

Total 6 issues

### Non-Critical Issues List
| Number |Issues Details| Context |
|:--:|:-------|:-------:|
| [N-01] |Use ```require``` instead of ```assert```| 1 |
| [N-02] | Include _return parameters_ in `NatSpec comments| All contracts |
| [N-03] | `0 address` check | 5 |
| [N-04] | Long lines are not suitable for the `Solidity Style Guide` | 3 |
| [N-05] | Undefined dependencies | 1 |
| [N-06] | For modern and more readable code; update import usages | 2 |
| [N-07] | Use scientific notation | 1 |
| [N-08] | Avoid the same variable names in contracts that receive inherit | 2 |
| [N-09] | Add to indexed parameter for countable Events | 4 |
| [N-10] | WETH address definition can be use directly | 1 |
| [N-11] | Empty blocks should be removed or Emit something | 1 |
| [N-12] | Open TODOs | 1 |

Total 12 issues

## [L-01] Missing Event for critical parameters change

**Context:**
[Vault.sol#L182-L193](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L182-L193)
[VaultFactory.sol#L248-L266](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L248-L266)

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

**Recommendation:**
Add ```event-emit```

## [L-02] Add to `Blacklist function`

**Description:**
Cryptocurrency mixing service, Tornado Cash, has been blacklisted in the OFAC.
A lot of blockchain companies, token projects, NFT Projects have ```blacklisted``` all Ethereum addresses owned by Tornado Cash listed in the US Treasury Department's sanction against the protocol.
https://home.treasury.gov/policy-issues/financial-sanctions/recent-actions/20220808
In addition, these platforms even ban accounts that have received ETH on their account with Tornadocash.

_**Some of these Projects;**_
- USDC (https://www.circle.com/en/usdc)
- Flashbots (https://www.paradigm.xyz/portfolio/flashbots )
- Aave (https://aave.com/)
- Uniswap
- Balancer
- Infura
- Alchemy 
- Opensea
- dYdX 

Details on the subject;
https://twitter.com/bantg/status/1556712790894706688?s=20&t=HUTDTeLikUr6Dv9JdMF7AA

https://cryptopotato.com/defi-protocol-aave-bans-justin-sun-after-he-randomly-received-0-1-eth-from-tornado-cash/

For this reason, every project in the Ethereum network must have a blacklist function, this is a good method to avoid legal problems in the future, apart from the current need.

Transactions from the project by an account funded by Tonadocash or banned by OFAC can lead to legal problems.Especially American citizens may want to get addresses to the blacklist legally, this is not an obligation

If you think that such legal prohibitions should be made directly by validators, this may not be possible:
https://www.paradigm.xyz/2022/09/base-layer-neutrality

```The ban on Tornado Cash makes little sense, because in the end, no one can prevent people from using other mixer smart contracts, or forking the existing ones. It neither hinders cybercrime, nor privacy.```

Here is the most beautiful and close to the project example; Manifold

Manifold Contract
https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```js
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```
Recommended Mitigation Steps add to Blacklist function and modifier.

## [L-03] Return value of transfer not checked

**Context:**
[Vault.sol#L167](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L167)
[Vault.sol#L228](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L228)
[Vault.sol#L365](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L365)

**Description:**
When there’s a failure in transfer()/transferFrom() the function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment.

**Recommendation:**
Add return value checks.

## [L-04] Use `abi.encode()` to avoid collision 

**Context:**
[Controller.sol#L180](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L180)
[Controller.sol#L236](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L236)

**Description:**
If you use keccak256(abi.encodePacked(a, b)) and both a and b are dynamic types, it is easy to craft collisions in the hash value by moving parts of a into b and vice-versa. More specifically, abi.encodePacked("a", "bc") == abi.encodePacked("ab", "c"). If you use abi.encodePacked for signatures, authentication or data integrity, make sure to always use the same types and check that at most one of them is dynamic. Unless there is a compelling reason, abi.encode should be preferred.

_**When are these used?**_
```abi.encode:```
abi.encode encode its parameters using the ABI specs. The ABI was designed to make calls to contracts.
Parameters are padded to 32 bytes. If you are making calls to a contract you likely have to use abi.encode
If you are dealing with more than one dynamic data type as it prevents collision.

```abi.encodePacked:```
abi.encodePacked encode its parameters using the minimal space required by the type. Encoding an uint8 it will use 1 byte. It is used when you want to save some space, and not calling a contract.Takes all types of data and any amount of input.

**Proof of Concept:**
```js
contract Contract0 {

    // abi.encode
    // (AAA,BBB) keccak = 0xd6da8b03238087e6936e7b951b58424ff2783accb08af423b2b9a71cb02bd18b
    // (AA,ABBB) keccak = 0x54bc5894818c61a0ab427d51905bc936ae11a8111a8b373e53c8a34720957abb
    function collision(string memory _text, string memory _anotherText)
        public pure returns (bytes32)
    {
        return keccak256(abi.encode(_text, _anotherText));
    }
}

contract Contract1 {

    // abi.encodePacked
    // (AAA,BBB) keccak = 0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358
    // (AA,ABBB) keccak = 0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358

    function hard(string memory _text, string memory _anotherText)
        public pure returns (bytes32)
    {
        return keccak256(abi.encodePacked(_text, _anotherText));
    }
}
```

## [L-05] Additional steps for srice feeds

**Description:**
By Chainlink, which the project uses in price feeds; to help you prepare for unforeseen market events, it is recommended to take additional steps to feeds to protect your practice or protocol.
There is no information in the documents about additional steps.
https://docs.chain.link/docs/selecting-data-feeds/#risk-mitigation

_**Risk Mitigation**_
As a development best practice, design your systems and smart contracts to be resilient and mitigate risk to your protocol and your users. Ensure that your systems can tolerate known and unknown exceptions that might occur. Some examples include but are not limited to volatile market conditions, the degraded performance of infrastructure, chains, or networks, and any other upstream outage related to data providers or node operators. You bear responsibility for any manner in which you use the Chainlink Network, its software and documentation.

To help you prepare for unforeseen market events, we recommend taking additional steps for custom or specialized feeds to protect your application or protocol. This might also be worth considering in all categories based on the value that your application secures. This tooling is put in place to mitigate extreme market events, possible malicious activity on third-party venues or contracts, potential delays, performance degradation, and outages.
Below are some examples of tooling that Chainlink users have put in place:

- Circuit breakers: In the case of an extreme price event, the contract would pause operations for a limited period of time.
- Contract update delays: Contracts would not update until the protocol had received a recent fresh input from the data feed.
- Manual kill switch: If a vulnerability or bug is discovered in one of the upstream contracts, the user can manually cease operation and temporarily sever the connection to the data feed.
- Monitoring: Some users create their own monitoring alerts based on deviations in the data feeds that they are using.
- Soak testing: Users are strongly advised to thoroughly test price feed integrations and incorporate a soak period prior to providing access to end users or securing value.

For more detailed information about some of these examples, see the Monitoring data feeds documentation.

For important updates regarding the use of Chainlink Price Feeds, users should join the official Chainlink Discord and subscribe to the data-feeds-user-notifications channel: https://discord.gg/Dqy5N9UbsR


## [L-06] No Check if onERC1155Received is implemented

**Context:**
[SemiFungibleVault.sol#L96](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L96)

**Description:**
The `SemiFungibleVault.sol`contract, function `deposit()`  will mint to `1155 NFT`. When minting a NFT, the function does not check if a receiving contract implements `onERC1155Received()`.

**Recommendation:**
The intention behind this function is to check if the address receiving the NFT, if it is a contract, implements `onERC1155Received()`. Thus, there is no check whether the _receiving address supports_ `ERC-1155` tokens and position could be not transferrable in some cases.
If this is intended, ```safemint``` should be used instead of ```mint```


## [N-01] Use ```require``` instead of ```assert```

**Context:**
[Vault.sol#L190](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L190)

**Description:**
Assert should not be used except for tests, require should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as request()/revert() did.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".


## [N-02] Include _return parameters_ in `NatSpec comments`

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**

Include return parameters in NatSpec comments.

_Recommendation  Code Style: (from Uniswap3)_
```js
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```

## [N-03] `0 address` check

**Context:**
[SemiFungibleVault.sol#L66](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L66)
[Vault.sol#L115](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L115)
[Vault.sol#L155](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L155)
[Vault.sol#L360](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L360)
[StakingRewards.sol#L73-L79](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/StakingRewards.sol#L73-L79)

**Description:**
Also check of the address to protect the code from 0x0 address  problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, you could add some good NatSpec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

**Recommendation:**
like this;
```if (_treasury == address(0)) revert ADDRESS_ZERO();```

## [N-04] Long lines are not suitable for the `Solidity Style Guide`

**Context:**
[VaultFactory.sol#L263](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L263)
[VaultFactory.sol#L272-L273](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L272-L273)
[RewardsFactory.sol#L83](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/rewards/RewardsFactory.sol#L83)

**Description:**
It is generally recommended that lines in the source code should not exceed 80-120 characters. Today's screens are much larger, so in some cases it makes sense to expand that. The lines above should be split when they reach that length, as the files will most likely be on GitHub and GitHub always uses a scrollbar when the length is more than 164 characters.

[why-is-80-characters-the-standard-limit-for-code-width](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width)


**Recommendation:**
Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction

```js
thisFunctionCallIsReallyLong(
    longArgument1,
    longArgument2,
    longArgument3
);
```

## [N-05] Undefined dependencies

**Context:**
[Vault.sol#L10](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L10)

**Description:**
OpenZeppelin's dependency " @openzeppelin/contracts/ " is not defined in package.json

```js
package.json : 
 {
  "scripts": {
    "solhint": "./node_modules/.bin/solhint -f table src/**/*.sol",
    "format": "prettier --write .",
    "format:check": "prettier --check '*/**/*.{js,sol,json,md,ts}'",
    "lint": "solhint 'src/**/*.sol'",
    "prettier": "prettier --write 'src/**/*.sol'",
    "prepare": "husky install"
  },
  "devDependencies": {
    "husky": "^8.0.0",
    "prettier": "^2.6.2",
    "prettier-plugin-solidity": "*",
    "scripty": "^2.1.0",
    "solhint": "^3.3.7"
  },
  "dependencies": {
    "solhint-plugin-prettier": "^0.0.5"
  }
}
```
**Recommendation:**
Add to OpenZeppelin dependencies in package.json


## [N-06] For modern and more readable code; update import usages

**Context:**
[Controller.sol#L5-L8](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Controller.sol#L5-L8)
[PegOracle.sol#L4](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L4)

**Description:**
Our Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct ```polluted the source code``` with an unnecessary object we were not using because we did not need it. This was breaking the rule of modularity and modular programming: ```only import what you need```Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
```import {contract1 , contract2} from "filename.sol";```


## [N-07] Use scientific notation

**Context:**
[PegOracle.sol#L78](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/oracles/PegOracle.sol#L78)

**Description:**
In its Major multiples, scientific notation should be used (eg: 1e5), not decimal notation (eg 100000) for readability.

**Recommendation:**
Use scientific notation.

## [N-08] Avoid the same variable names in contracts that receive inherit

**Context:**
[VaultFactory.sol#L15](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L15)
[Vault.sol#L35](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L35)

**Description:**
There is a state variable named ```treasury``` above, which is not a good practice in terms of code readability, it may cause problems with upgradability of the project.

**Recommendation:**
Use different State Variable names.


## [N-09] Add to indexed parameter for countable Events

**Context:**
[SemiFungibleVault.sol#L36](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L36)
[SemiFungibleVault.sol#L52](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L52)
[VaultFactory.sol#L51](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L51)
[VaultFactory.sol#L91](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L91)

**Description:**
Add to indexed parameter for countable Events

**Recommendation:**
Add Event-Emit


## [N-10] WETH address definition can be use directly

**Context:**
[VaultFactory.sol#L13](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/VaultFactory.sol#L13)

**Description:**
WETH is a wrap Ether contract with a specific address in the Etherum network, giving the option to define it may cause false recognition, it is healthier to define it directly.

Advantages of defining a specific contract directly:
- It saves gas,
- Prevents incorrect argument definition, 
- Prevents execution on a different chain and re-signature issues,

WETH Address : 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2

```js
constructor(address _manager, address _weth) payable initializer {
        manager = IManager(_manager);
        WETH = _weth;
    }
```

**Recommendation:**
```js
address private constant WETH = 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2;

    constructor(address _manager) payable initializer {
        manager = IManager(_manager);
}
```

## [N-11] Empty blocks should be removed or Emit something

**Context:**
[SemiFungibleVault.sol#L276-L286](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/SemiFungibleVault.sol#L276-L286)

**Description:**
Code contains empty block.

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


## [N-12] Open TODOs

**Context:**
[Vault.sol#L196](https://github.com/code-423n4/2022-09-y2k-finance/blob/main/src/Vault.sol#L196)

**Recommendation:**
Use temporary TODOs as you work on a feature, but make sure to treat them before merging. Either add a link to a proper issue in your TODO, or remove it from the code.

