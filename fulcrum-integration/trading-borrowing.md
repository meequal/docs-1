# Trading & Borrowing

### Overview

Trading \(taking a position\) involves first borrowing from an iToken. There are two ways to borrow using Fulcrum and open a position:

1. pToken contracts are tokenized versions of leveraged long or short positions on an asset. pTokens are minted by sending ETH or another asset into the pToken contract, which in turn borrows from an iToken lending pool, does a Kyber swap, and sends the user back an equivalent amount of pToken \(ERC20\) at the current pToken price. For short positions, such as 2x short ETH, the pToken borrows from the iETH token, then swaps that ETH for a stable asset \(DAI\) on KyberSwap. For long positions, such as 2x long ETH, the pToken borrows from from a stable asset, iDAI, and swaps that for ETH on KyberSwap. The tokens are perpetual, but positions are not. If a position loses too much value, or reaches the end of the loan term \(28 days\), it will be liquidated and a new position will be opened behind the scenes. Traders should be aware of the liquidation price of the pToken, and burn their pTokens prior to being liquidated, to avoid locking in any losses.
2. A trader can borrow directly from an iToken and manage their loan or margin trade themselves using the bZx protocol. The protocol supports margin loans that are under-collateralized or over-collatateralized. Over-collateralized loans, with 125% collateral or more, allow the user to withdraw the loan principal to their private wallet. The recommended way to borrow is to call the "borrowToken" function of the iToken contract.

The on-chain functions for interacting with pTokens will be described following.

## State-Changing Functions

### mintWithToken\(address,address,uint256\)

```text
function mintWithToken(
    address receiver,
    address depositTokenAddress,
    uint256 depositAmount)
    external
    returns (uint256);
```

Called to deposit assets to the pToken, which in turn mints pTokens to the lender’s wallet at the current tokenPrice\(\) rate. The receiver parameter specifies the address that will receive the minted pTokens. Any supported KyberToken can be specified \(depositTokenAddress\) to mint pTokens, however, specifying a token other the asset returned by the loanTokenAddress\(\) function will trigger a KyberSwap into the correct asset, being subject to any trade slippage that may occur. A prior ERC20 “approve” transaction should have been sent to the depositTokenAddress contract for an amount greater than or equal to depositAmount.

### mintWithEther\(address\)

```text
function mintWithEther(
    address receiver)
    external
    payable
    returns (uint256);
```

This accepts ETH sent directly to the pToken contract for minting pTokens. Mint actions for pTokens with "loanTokenAddress\(\)" assets other than WETH, will trigger a KyberSwap into the correct asset, as with the above.

### burnToToken\(address,address,uint256\)

```text
function burnToToken(
    address receiver,
    address burnTokenAddress,
    uint256 burnAmount)
    external
    returns (uint256);
```

Called to redeem owned pTokens for an equivalent amount of the underlying asset based on remaining collateral, unpaid interest, and including any profits or losses on the position, at the current tokenPrice\(\) rate. The receiver parameter specifies the address that will receive the asset proceeds.

### burnToEther\(address,uint256\)

```text
function burnToEther(
    address payable receiver,
    uint256 burnAmount)
    external
    returns (uint256);
```

Called to redeem owned pTokens similar to the above, but for an equivalent amount of ETH.

## Read-Only Functions

### tokenPrice\(\)

```text
function tokenPrice()
   public
   view
   returns (uint256 price);
```

Returns the current price of the pToken. Example 1000000000000000000000 = 1,000 pToken per ETH.

### liquidationPrice\(\)

```text
function liquidationPrice()
   public
   view
   returns (uint256 price);
```

Returns price at which the underlying position should be liquidated by the bZx protocol, or 0 if no position is open.

### checkpointPrice\(address\)

```text
function checkpointPrice(
    address _user)
    public
    view
    returns (uint256 price);
```

Returns the token price recorded during the last checkpoint for the user. Checkpoints occur whenever there is a token balance changing action taken by the user \(minting, burning, or transferring\). A user profit since last checkpoint would be calculated like so: \(tokenPrice\(\)-checkpointPrice\(user\)\) \_balanceOf\(user\) / 10\*\_36

### currentLeverage\(\)

```text
function currentLeverage()
    public
    view
    returns (uint256 leverage);
```

Returns the current leverage backing the underlying loan based on remaining collateral. Example 2000000000000000000 = 2x leverage.

### marketLiquidityForAsset\(\)

```text
function marketLiquidityForAsset()
    public
    view
    returns (uint256);
```

Returns the maximum amount of "loanTokenAddress\(\)" token that can be deposited to mint pTokens at the current time. This is based on borrow liquidity of the related iToken.

### marketLiquidityForToken\(\)

```text
function marketLiquidityForToken()
    public
    view
    returns (uint256);
```

Returns the maximum amount of pToken that can be minted at the current time. This is based on borrow liquidity of the related iToken, as well as the current pToken price \(tokenPrice\(\)\).

### assetBalanceOf\(address\)

```text
function assetBalanceOf(
    address _owner)
    public
    view
    returns (uint256);
```

Returns the user’s balance of the underlying asset. This is the same as multiplying the user’s token balance by the token price.

