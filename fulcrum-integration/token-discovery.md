# Token Registry / Contract ABIs

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

There are other read-only functions that may also be of use. The Etherscan-verified contracts for each network can be found at the follow addresses.

Mainnet: [registry.tokenloan.eth](https://etherscan.io/address/registry.tokenloan.eth#readContract)

Ropsten: 0xd03eea21041a19672e451bcbb413ce8be72d0381

Kovan: 0xF1C87dD61BF8a4e21978487e2705D52AA687F97E

## Contract ABIs

The latest ABIs \(Application Binary Interfaces\) for Fulcrum tokens and the TokenizedRegistry can be found here for each supported network: [https://fulcrum.trade/artifacts](https://fulcrum.trade/artifacts)

