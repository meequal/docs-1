# Token Discovery

## ENS Usage

Fulcrum token and related contracts make heavy use of [ENS Domains](https://ens.domains) on networks that support ENS \(Mainnet and Ropsten\).

The ENS convention for fulcrum tokens is \[symbol\].tokenloan.eth.

For instance: iETH.tokenloan.eth, iDAI.tokenloan.eth, psETH2x.tokenloan.eth, plwBTC4x.tokenloan.eth.

## Token Registry

Fulcrum provides a registry contract \(TokenizedRegistry\) that makes it easy to query which tokens are available on-chain. It stores a TokenMetadata struct for each token, which is returned as a tuple array, by calling the getTokens function.

```text
struct TokenMetadata {
    address token;
    address asset; // iToken -> loanToken, pToken -> tradeToken
    string name;
    string symbol;
    uint256 tokenType; // 0=no type set, 1=iToken, 2=pToken
    uint256 index;
}

function getTokens(
    uint256 _start, // starting index
    uint256 _count, // number of tokens to return
    uint256 _tokenType) // 0=no type set, 1=iToken, 2=pToken
    public
    view
    returns (TokenMetadata[] memory tokenData);
```

There are other view functions that may also be of use. The Etherscan-verified contracts for each network can be found at the follow addresses.

Mainnet: registry.tokenloan.eth \(coming soon\)

Ropsten: registry.tokenloan.eth \(coming soon\)

Rinkeby: \(coming soon\)

Kovan: 0x730df5c1e0a4b6ba7a982a585c1ec55187fbb3ca

## Contract Artifacts

The latest ABIs \(Application Binary Interfaces\) for Fulcrum tokens and the TokenizedRegistry can be found here for each supported network: [https://fulcrum.trade/artifacts](https://fulcrum.trade/artifacts)
