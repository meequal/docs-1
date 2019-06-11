# Lending

## Overview

Lending on Fulcrum is powered by iTokens, which are global lending pools. Each asset has a single iToken equivalent \(ETH as iETH, DAI has iDAI, etc.\), and has an independent interest rate paid to lenders who have deposited funds to the contract. The interest earned is proportional to the amount of iToken held by each lender. iTokens are minted by transferring the underlying asset to the contract, calling mint, and receiving back an equivalent amount of the iToken \(ERC20\) at the current iToken price. iTokens have an on-chain API to query current "redemption value" \(tokenPrice\(\)\), as well the interest rates paid by borrowers and paid to lenders.

## State-Changing Functions

### mint\(address,uint256\)

```text
function mint(
    address receiver,
    uint256 depositAmount)
    external
    returns (uint256 mintAmount);
```

Called to deposit assets to the iToken, which in turn mints iTokens to the lender’s wallet at the current tokenPrice\(\) rate. A prior ERC20 “approve” transaction should have been sent to the asset token for an amount greater than or equal to depositAmount. The receiver address specifies the address that will receive the minted iTokens. Note: The asset token address for an iToken is returned by the loanTokenAddress\(\) function. For iETH contracts, the expected underlying asset is WETH.

### mintWithEther\(address\)

```text
function mintWithEther(
    address receiver)
    external
    payable
    returns (uint256 mintAmount);
```

\(iETH only\) This accepts ETH sent directly to the iETH contract for minting iETH tokens.

### burn\(address,uint256\)

```text
function burn(
    address receiver,
    uint256 burnAmount)
    external
    returns (uint256 loanAmountPaid);
```

Called to redeem owned iTokens for an equivalent amount of the underlying asset, at the current tokenPrice\(\) rate. The receiver address specifies the address that will receive the asset proceeds.

### burnToEther\(address\)

```text
function burnToEther(
    address payable receiver,
    uint256 burnAmount)
    external
    returns (uint256 loanAmountPaid);
```

\(iETH only\) Called to redeem owned iTokens similar to the above, but for an equivalent amount of ETH.

### claimLoanToken\(\)

```text
function claimLoanToken()
    external
    returns (uint256 claimedAmount);
```

In the event that there isn't available \(un-borrowed\) liquidity after a burn\(\) function is called, the lender is entered into a "reserve" queue to be automatically paid back as liquidity becomes freed from borrowers, when borrowers close their positions or a position gets liquidated. At anytime, a lender can call this function to claim any available funds owed to them, and to put them in the front of the reserve queue.

### borrowToken\(uint256,uint256,address,address,bool\)

```text
function borrowToken(
    uint256 borrowAmount,
    uint256 leverageAmount,
    address collateralTokenAddress,
    address tradeTokenToFillAddress,
    bool withdrawOnOpen)
    external
    returns (uint256);
```

This function can be called by a borrower to open a bZx base protocol loan for margin trading or borrowing of a specified amount \(borrowAmount\) and leverage \(leverageAmount\). The loan will conform to the parameters set for the iToken, such as loan length \(typically 28 days\). Interest required from the borrower is determined based on the interest rate returned by the nextLoanInterestRate\(uint256\) function and is denominated the same as the asset being borrowed \(loanTokenAddress\(\)\). The borrower is permitted to collateralize \(collateralTokenAddress\) their loan with any token supported for lending and trading on Fulcrum. If the trader desires to immediately open a position with the borrowed asset \(short the asset\), they can specify another supported token for tradeTokenToFillAddress, otherwise the borrowers should specify a 0 address. A TRUE value for withdrawOnOpen indicates the loan will be over-collateralized, and the borrowed asset will be immediately deposited to the borrowers wallet on loan open. If withdrawOnOpen, any value for tradeTokenToFillAddress is ignored. Users of this function should have the correct approvals set on the BZxVault base protocol contract. Token approvals can be verified on the bZx Portal Balances page \([https://portal.bzx.network](https://portal.bzx.network)\).

### borrowTokenFromEscrow\(uint256,uint256,address,bool\)

```text
function borrowTokenFromEscrow(
    uint256 escrowAmount,
    uint256 leverageAmount,
    address tradeTokenToFillAddress,
    bool withdrawOnOpen)
    external
    returns (uint256);
```

This function behaves similarly to the borrowToken function, but instead accepts an escrowAmount, which will be correctly split to cover both the required collateral + interest for the loan, based on the specified leverageAmount and the current interest rate. The borrowAmount of the loan will be calculated as the maximum amount that this escrowAmount can support. The token for collateral and interest tokens are denominated the same as the asset being borrowed \(loanTokenAddress\(\)\).

## Read-Only Functions

### tokenPrice\(\)

```text
function tokenPrice()
   public
   view
   returns (uint256 price);
```

Returns the current price of the iToken. Example 1000000000000000000 = 1 ETH per iToken.

### checkpointPrice\(address\)

```text
function checkpointPrice(
    address _user)
    public
    view
    returns (uint256 price);
```

Returns the token price recorded during the last checkpoint for the user. Checkpoints occur whenever there is a token balance changing action taken by the user \(minting, burning, transferring, or claiming\). A user profit since last checkpoint would be calculated like so: \(tokenPrice\(\)-checkpointPrice\(user\)\) \* balanceOf\(user\) / 10^36

### totalReservedSupply\(\)

```text
function totalReservedSupply()
    public
    view
    returns (uint256);
```

Returns amount of assets that are reserved for redemption by lenders and unavailable for future borrowing. Some or all of these funds may still be locked in current loans.

### marketLiquidity\(\)

```text
function marketLiquidity()
    public
    view
    returns (uint256);
```

Returns amount of assets available for borrowing from the iToken lending pool \(totalAssetSupply\(\)-totalAssetBorrow\(\)\)

### borrowInterestRate\(\)

```text
function borrowInterestRate()
    public
    view
    returns (uint256);
```

Returns the aggregate rate that all borrowers are paying to iToken holders.

### supplyInterestRate\(\)

```text
function supplyInterestRate()
    public
    view
    returns (uint256);
```

Returns the aggregate rate that all lenders are receiving from iToken borrowers. The supplyInterestRate\(\) will always be less than the borrowInterestRate\(\). Please refer to the Interest Determination section of the [Fulcrum announcement](https://medium.com/bzxnetwork/introducing-fulcrum-tokenized-margin-made-dead-simple-e65ccc82393f) article for additional information.

### nextLoanInterestRate\(uint256\)

```text
function nextLoanInterestRate(
    uint256 borrowAmount)
    public
    view
    returns (uint256);
```

For non-pToken borrowers, returns interest rate a borrower would pay for opening a loan against the iToken. The rate increases proportional to the borrowAmount.

### interestReceived\(\)

```text
function interestReceived()
    public
    view
    returns (uint256 interestTotalAccrued);
```

Returns the total amount of interest earned for all active loans.

### totalAssetSupply\(\)

```text
function totalAssetSupply()
    public
    view
    returns (uint256);
```

Returns the total amount assets currently being supplied for lending, which includes earned interest.

### totalAssetBorrow\(\)

```text
function totalAssetBorrow()
    public
    view
    returns (uint256);
```

Returns the total amount assets currently borrowed in active loans.

### getMaxEscrowAmount\(uint256\)

```text
function getMaxEscrowAmount(
    uint256 leverageAmount)
    public
    view
    returns (uint256);
```

Returns the maximum amount of escrow the borrower can submit to open a loan for a given leverageAmount.

### getBorrowAmount\(uint256,uint256,bool\)

```text
function getBorrowAmount(
    uint256 escrowAmount,
    uint256 leverageAmount,
    bool withdrawOnOpen)
    public
    view
    returns (uint256);
```

Returns the amount of loan principal received for a given borrower escrow amount and leverageAmount. A TRUE value for withdrawOnOpen indicates the loan will be over-collateralized and withdrawable to the borrower’s private wallet.

### getLoanData\(uint256\)

```text
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

### getLeverageList\(\)

```text
function getLeverageList()
    public
    view
    returns (uint256[] memory);
```

Returns a list of leverage amounts supported by the iToken. An example leverage: 2000000000000000000 = 2x leverage

### assetBalanceOf\(address\)

```text
function assetBalanceOf(
    address _owner)
    public
    view
    returns (uint256);
```

Returns the user’s balance of the underlying asset. This is the same as multiplying the user’s token balance by the token price.

