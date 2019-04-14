# Fulcrum Integration Guide

## Intro

The Fulcrum portal (https://fulcrum.trade) is a front-end web interface that is the first and only completely trustless platform for margin; it does not use centralized price feeds or centrally administered margin calls. It is permissionless and rent free. There are no fees and no accounts to setup. Fulcrum is built on the bZx base protocol and extends the protocol by allowing both loans and margin positions to be tokenized. Tokenized loans, called iTokens, are for lending and earn interest. Tokenized positions, called pTokens, are for gaining long or short market exposure to an asset and let the user trade with borrowed funds and with leverage. Both iTokens and pTokens are ERC20-compliant and provide access to the view functions symbol, name, decimals, totalSupply, balanceOf, and allowance, as well as the non-payable functions transfer, transferFrom, approve, increaseApproval, and decreaseApproval. Collectively, we'll refer to both iTokens and pTokens as "Fulcrum tokens".

## Fulcrum Token Discovery

### ENS Usage

Fulcrum token and related contracts make heavy use of ENS Domains on networks that support ENS (Mainnet and Ropsten).

The ENS convention for fulcrum tokens is [symbol].tokenloan.eth.

For instance: iETH.tokenloan.eth, iDAI.tokenloan.eth, psETH2x.tokenloan.eth, plwBTC4x.tokenloan.eth.

### Token Registry

Fulcrum provides a registry contract (TokenizedRegistry) that makes it easy to query which tokens are available on-chain. It stores a TokenMetadata struct for each token, which is returned as a tuple array, by calling the getTokens function.
```
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

Mainnet: registry.tokenloan.eth (coming soon)
Ropsten: registry.tokenloan.eth (coming soon)
Rinkeby: (coming soon)
Kovan: 0x730df5c1e0a4b6ba7a982a585c1ec55187fbb3ca

## Lending

### Overview
Lending on Fulcrum is powered by iTokens, which are global lending pools. Each asset has a single iToken equivalent (ETH as iETH, DAI has iDAI, etc.), and has an independent interest rate paid to lenders who have deposited funds to the contract. The interest earned is proportional to the amount of iToken held by each lender. iTokens are minted by transferring the underlying asset to the contract, calling mint, and receiving back an equivalent amount of the iToken (ERC20) at the current iToken price. iTokens have an on-chain API to query current "redemption value" (tokenPrice()), as well the interest rates paid by borrowers and paid to lenders. The on-chain functions for interacting with iTokens will be described following.

### mint(address,uint256)
```
function mint(
	address receiver,
	uint256 depositAmount)
	external
	returns (uint256 mintAmount);
```
Called to deposit assets to the iToken, which in turn mints iTokens to the lender’s wallet at the current tokenPrice() rate. A prior ERC20 “approve” transaction should have been sent to the asset token for an amount greater than or equal to depositAmount. The receiver address specifies the address that will receive the minted iTokens. Note: The asset token address for an iToken is returned by the loanTokenAddress() function. For iETH contracts, the expected underlying asset is WETH.

### mintWithEther(address)
```
function mintWithEther(
	address receiver)
	external
	payable
	returns (uint256 mintAmount);
```
(iETH only) This accepts ETH sent directly to the iETH contract for minting iETH tokens.

### burn(address,uint256)
```
function burn(
	address receiver,
	uint256 burnAmount)
	external
	returns (uint256 loanAmountPaid);
```
Called to redeem owned iTokens for an equivalent amount of the underlying asset, at the current tokenPrice() rate. The receiver address specifies the address that will receive the asset proceeds.

### burnToEther(address)
```
function burnToEther(
	address payable receiver,
	uint256 burnAmount)
	external
	returns (uint256 loanAmountPaid);
```
(iETH only) Called to redeem owned iTokens similar to the above, but for an equivalent amount of ETH.

### claimLoanToken()
```
function claimLoanToken()
	external
	returns (uint256 claimedAmount);
```
In the event that there isn't available (un-borrowed) liquidity after a burn() function is called, the lender is entered into a "reserve" queue to be automatically paid back as liquidity becomes freed from borrowers, when borrowers close their positions or a position gets liquidated. At anytime, a lender can call this function to claim any available funds owed to them, and to put them in the front of the reserve queue.

### borrowToken(uint256,uint256,address,address,bool)
```
function borrowToken(
	uint256 borrowAmount,
	uint256 leverageAmount,
	address collateralTokenAddress,
	address tradeTokenToFillAddress,
	bool withdrawOnOpen)
	external
	returns (uint256);
```
This function can be called by a borrower to open a bZx base protocol loan for margin trading or borrowing of a specified amount (borrowAmount) and leverage (leverageAmount). The loan will conform to the parameters set for the iToken, such as loan length (typically 28 days). Interest required from the borrower is determined based on the interest rate returned by the nextLoanInterestRate(uint256) function and is denominated the same as the asset being borrowed (loanTokenAddress()). The borrower is permitted to collateralize their loan with any supported KyberSwap token (collateralTokenAddress). If the trader desires to immediately open a position with the borrowed asset (short the asset), they can specified another token for tradeTokenToFillAddress, otherwise the borrowers should specify a 0 address. A TRUE value for withdrawOnOpen indicates the loan will be over-collateralized, and the borrowed asset will be immediately deposited to the borrowers wallet on loan open. If withdrawOnOpen, any value for tradeTokenToFillAddress is ignored. Users of this function should have the correct approvals set on the BZxVault base protocol contract. Token approvals can be verified on the bZx Portal Balances page (https://portal.bzx.network).

Note: Prior to calling this function, the iToken contract must be approved as a "delegate" in the bZx Protocol for opening loans on your behalf. The iToken contract can only open loans when you specifically request it to by a later call to "borrowToken" or "borrowTokenFromEscrow". The following function should be called on the base protocol (Mainnet contract: bzxnetwork.eth).
```
function toggleDelegateApproved(
	address delegate,
	bool isApproved)
	external;
```
To enable delegation, pass the iToken contract address as "delegate" and "TRUE" for isApproved. Delegation remains in effect unless the user calls this function again to disable delegation with a "FALSE" value.

There is no need to call "toggleDelegateApproved" when using pTokens. This function is only needed when borrowing directly against an iToken for a base protocol loan. 

### borrowTokenFromEscrow(uint256,uint256,address,bool)
```
function borrowTokenFromEscrow(
	uint256 escrowAmount,
	uint256 leverageAmount,
	address tradeTokenToFillAddress,
	bool withdrawOnOpen)
	external
	returns (uint256);
```
This function behaves similarly to the borrowToken function, but instead accepts an escrowAmount, which will be correctly split to cover both the required collateral + interest for the loan, based on the specified leverageAmount and the current interest rate. The borrowAmount of the loan will be calculated as the maximum amount that this  escrowAmount can support. The token for collateral and interest tokens are denominated the same as the asset being borrowed (loanTokenAddress()).

Note: Please reference the above note on "toggleDelegateApproved". The same applies here.

### tokenPrice()
```
function tokenPrice()
   public
   view
   returns (uint256 price);
```
Returns the current price of the iToken. Example 1000000000000000000 = 1 iToken per ETH.

### checkpointPrice(address)
```
function checkpointPrice(
	address _user)
	public
	view
	returns (uint256 price);
```
Returns the token price recorded during the last checkpoint for the user. Checkpoints occur whenever there is a token balance changing action taken by the user (minting, burning, transferring, or claiming). A user profit since last checkpoint would be calculated like so: (tokenPrice()-checkpointPrice(user)) * balanceOf(user) / 10**36

### totalReservedSupply()
```
function totalReservedSupply()
	public
	view
	returns (uint256);
```
Returns amount of assets that are reserved for redemption by lenders and unavailable for future borrowing. Some or all of these funds may still be locked in current loans.

### marketLiquidity()
```
function marketLiquidity()
	public
	view
	returns (uint256);
```
Returns amount of assets available for borrowing from the iToken lending pool (totalAssetSupply()-totalAssetBorrow())

### borrowInterestRate()
```
function borrowInterestRate()
	public
	view
	returns (uint256);
```
Returns the aggregate rate that all borrowers are paying to iToken holders.

### supplyInterestRate()
```
function supplyInterestRate()
	public
	view
	returns (uint256);
```
Returns the aggregate rate that all lenders are receiving from iToken borrowers. The supplyInterestRate() will always be less than the borrowInterestRate(). Please refer to the Interest Determination section of the [Fulcrum announcement](https://medium.com/bzxnetwork/introducing-fulcrum-tokenized-margin-made-dead-simple-e65ccc82393f) article for additional information.

### nextLoanInterestRate(uint256)
```
function nextLoanInterestRate(
	uint256 borrowAmount)
	public
	view
	returns (uint256);
```
For non-pToken borrowers, returns interest rate a borrower would pay for opening a loan against the iToken. The rate increases proportional to the borrowAmount.

### interestReceived()
```
function interestReceived()
	public
	view
	returns (uint256 interestTotalAccrued);
```
Returns the total amount of interest earned for all active loans.

### totalAssetSupply()
```
function totalAssetSupply()
	public
	view
	returns (uint256);
```
Returns the total amount assets currently being supplied for lending, which includes earned interest.

### totalAssetBorrow()
```
function totalAssetBorrow()
	public
	view
	returns (uint256);
```
Returns the total amount assets currently borrowed in active loans.

### getMaxEscrowAmount(uint256)
```
function getMaxEscrowAmount(
	uint256 leverageAmount)
	public
	view
	returns (uint256);
```
Returns the maximum amount of escrow the borrower can submit to open a loan for a given leverageAmount.

### getBorrowAmount(uint256,uint256,bool)
```
function getBorrowAmount(
	uint256 escrowAmount,
	uint256 leverageAmount,
	bool withdrawOnOpen)
	public
	view
	returns (uint256);
```
Returns the amount of loan principal received for a given borrower escrow amount and leverageAmount. A TRUE value for withdrawOnOpen indicates the loan will be over-collateralized and withdrawable to the borrower’s private wallet.

### getLoanData(uint256)
```
struct LoanData {
	bytes32 loanOrderHash;
	uint256 leverageAmount;
	uint256 initialMarginAmount;
	uint256 maintenanceMarginAmount;
	uint256 index;
}

function getLoanData(
	uint256 levergeAmount)
	public
	view
	returns (LoanData memory);
```
Returns a LoanData object with data for a given leverageAmount.

### getLeverageList()
```
function getLeverageList()
	public
	view
	returns (uint256[] memory);
```
Returns a list of leverage amounts supported by the iToken. An example leverage: 2000000000000000000 = 2x leverage

### assetBalanceOf(address)
```
function assetBalanceOf(
	address _owner)
	public
	view
	returns (uint256);
```
Returns the user’s balance of the underlying asset. This is the same as multiplying the user’s token balance by the token price.

## Trading and Borrowing

### Overview
Trading (taking a position) involves first borrowing from an iToken. There are two ways to borrow using Fulcrum and open a position:

1. pToken contracts are tokenized versions of leveraged long or short positions on an asset. pTokens are minted by sending ETH or another asset into the pToken contract, which in turn borrows from an iToken lending pool, does a Kyber swap, and sends the user back an equivalent amount of pToken (ERC20) at the current pToken price. For short positions, such as 2x short ETH, the pToken borrows from the iETH token, then swaps that ETH for a stable asset (DAI) on KyberSwap. For long positions, such as 2x long ETH, the pToken borrows from from a stable asset, iDAI, and swaps that for ETH on KyberSwap. The tokens are perpetual, but positions are not. If a position loses too much value, or reaches the end of the loan term (28 days), it will be liquidated and a new position will be opened behind the scenes. Traders should be aware of the liquidation price of the pToken, and burn their pTokens prior to being liquidated, to avoid locking in any losses.

2. A trader can borrow directly from an iToken and manage their loan or margin trade themselves using the bZx protocol. The protocol supports margin loans that are under-collateralized or over-collatateralized. Over-collateralized loans, with 125% collateral or more, allow the user to withdraw the loan principal to their private wallet. The recommended way to borrow is to call the "borrowToken" function of the iToken contract.

The on-chain functions for interacting with pTokens will be described following.

### mintWithToken(address,address,uint256)
```
function mintWithToken(
	address receiver,
	address depositTokenAddress,
	uint256 depositAmount)
	external
	returns (uint256);
```
Called to deposit assets to the pToken, which in turn mints pTokens to the lender’s wallet at the current tokenPrice() rate. The receiver parameter specifies the address that will receive the minted pTokens. Any supported KyberToken can be specified (depositTokenAddress) to mint pTokens, however, specifying a token other the asset returned by the loanTokenAddress() function will trigger a KyberSwap into the correct asset, being subject to any trade slippage that may occur. A prior ERC20 “approve” transaction should have been sent to the depositTokenAddress contract for an amount greater than or equal to depositAmount. 

### mintWithEther(address)
```
function mintWithEther(
	address receiver)
	external
	payable
	returns (uint256);
```
This accepts ETH sent directly to the pToken contract for minting pTokens. Mint actions for pTokens with "loanTokenAddress()" assets other than WETH, will trigger a KyberSwap into the correct asset, as with the above.

### burnToToken(address,address,uint256)
```
function burnToToken(
	address receiver,
	address burnTokenAddress,
	uint256 burnAmount)
	external
	returns (uint256);
```
Called to redeem owned pTokens for an equivalent amount of the underlying asset based on remaining collateral, unpaid interest, and including any profits or losses on the position, at the current tokenPrice() rate. The receiver parameter specifies the address that will receive the asset proceeds.

### burnToEther(address,uint256)
```
function burnToEther(
	address payable receiver,
	uint256 burnAmount)
	external
	returns (uint256);
```
Called to redeem owned pTokens similar to the above, but for an equivalent amount of ETH.

### tokenPrice()
```
function tokenPrice()
   public
   view
   returns (uint256 price);
```
Returns the current price of the pToken. Example 1000000000000000000000 = 1,000 pToken per ETH.

### liquidationPrice()
```
function liquidationPrice()
   public
   view
   returns (uint256 price);
```
Returns price at which the underlying position should be liquidated by the bZx protocol, or 0 if no position is open.

### checkpointPrice(address)
```
function checkpointPrice(
	address _user)
	public
	view
	returns (uint256 price);
```
Returns the token price recorded during the last checkpoint for the user. Checkpoints occur whenever there is a token balance changing action taken by the user (minting, burning, or transferring). A user profit since last checkpoint would be calculated like so: (tokenPrice()-checkpointPrice(user)) * balanceOf(user) / 10**36

### currentLeverage()
```
function currentLeverage()
	public
	view
	returns (uint256 leverage);
```
Returns the current leverage backing the underlying loan based on remaining collateral. Example 2000000000000000000 = 2x leverage.

### marketLiquidityForAsset()
```
function marketLiquidityForAsset()
	public
	view
	returns (uint256);
```
Returns the maximum amount of "loanTokenAddress()" token that can be deposited to mint pTokens at the current time. This is based on borrow liquidity of the related iToken.

### marketLiquidityForToken()
```
function marketLiquidityForToken()
	public
	view
	returns (uint256);
```
Returns the maximum amount of pToken that can be minted at the current time. This is based on borrow liquidity of the related iToken, as well as the current pToken price (tokenPrice()).

### assetBalanceOf(address)
```
function assetBalanceOf(
	address _owner)
	public
	view
	returns (uint256);
```
Returns the user’s balance of the underlying asset. This is the same as multiplying the user’s token balance by the token price.

## Contract Artifacts

The latest ABIs (Application Binary Interfaces) for Fulcrum tokens and the TokenizedRegistry can be found here for each supported network:
https://fulcrum.trade/artifacts
