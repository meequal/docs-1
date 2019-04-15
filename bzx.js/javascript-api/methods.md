# Methods

## Allowance

### getAllowance

Get the amount of tokens at `params.tokenAddress` granted to withdraw by `params.spenderAddress` from `params.ownerAddress`.

```typescript
  getAllowance(params: {
    tokenAddress: string;
    ownerAddress: string;
    spenderAddress: string;
  }): Promise<BigNumber>;
```

**Arguments**

`params.tokenAddress` ERC20 contract address of the token

`params.ownerAddress` address of the owner of the tokens - wallet who grants right to withdraw tokens

`params.spenderAddress` address of the spender of the tokens - wallet who gets right to withdraw tokens

**Returns**

`BigNumber` value indicating tokens amount allowed to withdraw

### setAllowance

Allow `params.spenderAddress` to withdraw tokens at `params.tokenAddress` from `params.ownerAddress`, multiple times, up to the `params.amountInBaseUnits` amount.

```typescript
  setAllowance(params: {
    tokenAddress: string;
    ownerAddress: string;
    spenderAddress: string;
    amountInBaseUnits: BigNumber | string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.tokenAddress` ERC20 contract address of the token

`params.ownerAddress` address of the owner of the tokens - wallet who grants right to use tokens

`params.spenderAddress` address of the spender of the tokens - wallet who gets right to use tokens

`params.amountInBaseUnits` amount of tokens that are allowed to use

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### setAllowanceUnlimited

Allow `params.spenderAddress` to withdraw tokens from `params.ownerAddress`, multiple times, without the limit.

```typescript
  setAllowanceUnlimited(params: {
    tokenAddress: string;
    ownerAddress: string;
    spenderAddress: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.tokenAddress` ERC20 contract address of the token

`params.ownerAddress` address of the owner of the tokens - wallet who grants right to use tokens

`params.spenderAddress` address of the spender of the tokens - wallet who gets right to use tokens

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### resetAllowance

Disallow `params.spenderAddress` to withdraw tokens from `params.ownerAddress`.

```typescript
  resetAllowance(params: {
    tokenAddress: string;
    ownerAddress: string;
    spenderAddress: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.tokenAddress` ERC20 contract address of the token

`params.ownerAddress` address of the owner of the tokens - wallet who grants right to use tokens

`params.spenderAddress` address of the spender of the tokens - wallet who gets right to use tokens

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

## Hash

### getLoanOrderHashHex

Calculate Keccak-256 hash of loan order with specified parameters.

```typescript
  getLoanOrderHashHex(order: ILoanOrderFillRequest): string;
```

**Arguments**

`order` loan order

**Returns**

Hash value `string`

### getLoanOrderHashAsync

Calculate Keccak-256 hash of order with specified parameters.

```typescript
  getLoanOrderHashAsync(order: ILoanOrderFillRequest): Promise<string>;
```

**Arguments**

`order` loan order

**Returns**

`Promise` for hash value `string`

## Signature

### signOrderHashAsync

Sign loan order and returns the signature `string`.

```typescript
  signOrderHashAsync(
    orderHash: string,
    signerAddress: string,
    shouldAddPersonalMessagePrefix: boolean
  ): Promise<string>;
```

**Arguments**

`orderHash` loan order hash

`signerAddress` address of a wallet signing the loan order hash

`shouldAddPersonalMessagePrefix` indicates whether or not personal message prefix should be added

**Returns**

`Promise` for `string` containing order hash signature

### isValidSignature

Check order hash signature validity.

```typescript
  static isValidSignature(params: {
    account: string;
    orderHash: string;
    signature: string;
  }): boolean;
```

**Arguments**

`params.account` account who signed the loan order hash

`params.orderHash` loan order hash

`params.signature` loan order signature

**Returns**

`boolean` value indicating if the signature is valid

### isValidSignatureAsync

Check order hash signature validity.

```typescript
  isValidSignatureAsync(params: {
    account: string;
    orderHash: string;
    signature: string;
  }): Promise<boolean>;
```

**Arguments**

`params.account` account who signed the loan order hash

`params.orderHash` loan order hash

`params.signature` loan order signature

**Returns**

`Promise` for `boolean` value indicating if the signature is valid

## Exchange

### getTokenList

Provide metadata for all registered tokens.

```typescript
  getTokenList(): Promise<ITokenDescription[]>;
```

**Arguments**

None

**Returns**

`Promise` for an array of `ITokenDescription`

### getOracleList

Provide metadata for all registered oracles.

```typescript
  getOracleList(): Promise<IOracleDescription[]>;
```

**Arguments**

None

**Returns**

`Promise` for an array of `IOracleDescription`

### isTradeSupported

Check if specified `params.oracleAddress` supports exchange operation of provided tokens.

```typescript
  isTradeSupported(params: { 
    sourceTokenAddress: string; 
    sourceTokenAmount: string;
    destTokenAddress: string; 
    oracleAddress: string;
  }): Promise<boolean>;
```

**Arguments**

`sourceTokenAddress` address of source token's ERC20 contract

`sourceTokenAmount` amount of source tokens to exchange

`destTokenAddress` address of destination token's ERC20 contract

`oracleAddress` address of the oracle to check tokens pair support

**Returns**

`Promise` for `boolean` value indicating if oracle is able to make exchange operation between tokens

### getConversionData

Get terms of exchange operation between tokens in the specific amount using selected oracle.

```typescript
  getConversionData(params: {
    sourceTokenAddress: string;
    sourceTokenAmount: string;
    destTokenAddress: string;
    oracleAddress: string;
  }): Promise<IConversionData>;
```

**Arguments**

`sourceTokenAddress` address of source token's ERC20 contract

`sourceTokenAmount` amount of source tokens to exchange

`destTokenAddress` address of destination token's ERC20 contract

`oracleAddress` address of oracle to check tokens pair support

**Returns**

`Promise` for `IConversionData` value containing exchange rate and available amount of tokens for exchange

## Loan orders

### getSingleOrder

Get single loan order by it's `params.loanOrderHash`.

```typescript
  getSingleOrder(params: { 
    loanOrderHash: string;
  }): Promise<ILoanOrderFillable>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

**Returns**

`Promise` for `ILoanOrderFillable` which represents the current fill state of specified loan order

### getOrdersFillable

Get the list of loan orders that are available for taking.

```typescript
  getOrdersFillable(params: { 
    start: number; 
    count: number;
    oracleFilter?: string;
  }): Promise<ILoanOrderFillable[]>;
```

**Arguments**

`params.start` starting number of the loan order in the list of orders that are available for taking

`params.count` maximum number of loan orders to return

`params.oracleFilter` oracleAddress to filter list of loan orders

**Returns**

`Promise` for an array of `ILoanOrderFillable` every item of which represents the current fill state of specified loan order

### getOrdersForUser

Return the list of loan orders filtered by specified `params.loanPartyAddress`.

```typescript
  getOrdersForUser(params: {
    loanPartyAddress: string; 
    start: number;
    count: number;
    oracleFilter?: string;
  }): Promise<ILoanOrderFillable[]>;
```

**Arguments**

`params.loanPartyAddress` the address of the lender/trader in the loan order

`params.start` starting number of the loan order in the list of orders

`params.count` maximum number of loan orders to return

`params.oracleFilter` oracleAddress to filter list of loan orders

**Returns**

`Promise` for an array of `ILoanOrderFillable` every item of which represents the current fill state of specified loan order

### takeLoanOrderAsTrader

Take loan order created and signed by the lender and push it on-chain.

```typescript
  takeLoanOrderAsTrader(params: {
    order: ILoanOrderFillRequest;
    oracleData: any;
    collateralTokenAddress: string;
    loanTokenAmountFilled: BigNumber;
    tradeTokenToFillAddress: string;
    withdrawOnOpen: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.order` signed loan order `ILoanOrderFillRequest`

`params.oracleData` information specific for selected oracle. for bZxOracle it should be empty string, for augur oracle this should be augur oracle market.

`params.collateralTokenAddress` desired address of the collateral the trader wants to use

`params.loanTokenAmountFilled` desired amount of loanToken the trader wants to borrow

`params.tradeTokenToFillAddress` if `"0"` then nothing happens, if non-zero, than a trade is made with the oracle once the loan is opened

`params.withdrawOnOpen` if `true`, then over-collateralize \(initial margin + 100% of loan value\), and withdraw the loan token to the trader's wallet  
if `false`, that normal margin loan \(initial margin collateral only\) NOTE: if true, than `params.tradeTokenToFillAddress` is ignored and assumed to be equal to `"0`

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### takeLoanOrderAsLender

Take loan order created and signed by the trader and push it on-chain.

```typescript
  takeLoanOrderAsLender(params: {
    order: ILoanOrderFillRequest;
    oracleData: any;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.order` signed loan order `ILoanOrderFillRequest`

`params.oracleData` information specific for selected oracle. for bZxOracle it should be empty string, for augur oracle this should be augur oracle market.

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### pushLoanOrderOnChain

Push signed loan order on-chain.

```typescript
  pushLoanOrderOnChain(params: {
    order: ILoanOrderFillRequest;
    oracleData: any;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.order` signed loan order `ILoanOrderFillRequest`

`params.oracleData` information specific for selected oracle. for bZxOracle it should be empty string, for augur oracle this should be augur oracle market.

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### takeLoanOrderOnChainAsTrader

Take loan order created and signed by the lender and already located on-chain \(partially filled\).

```typescript
  takeLoanOrderOnChainAsTrader(params: {
    loanOrderHash: string;
    collateralTokenAddress: string;
    loanTokenAmountFilled: BigNumber;
    tradeTokenToFillAddress: string;
    withdrawOnOpen: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.collateralTokenAddress` desired address of the collateral the trader wants to use

`params.loanTokenAmountFilled` desired amount of loanToken the trader wants to borrow

`params.tradeTokenToFillAddress` if `"0"` then nothing happens, if non-zero, than a trade is made with the oracle once the loan is opened

`params.withdrawOnOpen` if `true`, then over-collateralize \(initial margin + 100% of loan value\), and withdraw the loan token to the trader's wallet  
if `false`, that normal margin loan \(initial margin collateral only\) NOTE: if true, than `params.tradeTokenToFillAddress` is ignored and assumed to be equal to `"0`

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### takeLoanOrderOnChainAsLender

Take loan order created and signed by the trader and already located on-chain \(partially filled\).

```typescript
  takeLoanOrderOnChainAsLender(params: {
    loanOrderHash: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### getSingleLoan

Get the loan order current execution state.

```typescript
  getSingleLoan(params: { 
    loanOrderHash: string;
    trader: string 
  }): Promise<ILoanPositionState>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.trader` the address of the lender/trader in the loan order

**Returns**

`Promise` for `ILoanPositionState` which represents the current state of specified loan order

### getLoansForLender

Get the list of loan orders with current execution state filtered by lender `params.address`.

```typescript
  getLoansForLender(params: { 
    address: string; 
    count: number; 
    activeOnly: boolean 
  }): Promise<ILoanPositionState[]>;
```

**Arguments**

`params.address` the address of the lender in the loan order

`params.count` maximum number of loan orders to return

`params.activeOnly` should this function return loan orders in active only \(`true`\) or in any state \(`false`\)

**Returns**

`Promise` for an array of `ILoanPositionState` every item of which represents the current state of related loan order

### getLoansForTrader

Get the list of loan orders with current execution state filtered by trader `params.address`.

```typescript
  getLoansForTrader(params: { 
    address: string; 
    count: number; 
    activeOnly: boolean 
  }): Promise<ILoanPositionState[]>;
```

**Arguments**

`params.address` the address of the trader in the loan order

`params.count` maximum number of loan orders to return

`params.activeOnly` should this function return loan orders in active only \(`true`\) or in any state \(`false`\)

**Returns**

`Promise` for an array of `ILoanPositionState` every item of which represents the current state of related loan order

### getActiveLoans

Get the paginated list of active loan orders.

```typescript
  getActiveLoans(params: { 
    start: number; 
    count: number 
  }): Promise<ILoanOrderActive[]>;
```

**Arguments**

`params.start` starting number of the loan order in the list of active orders

`params.count` maximum number of loan orders to return

**Returns**

`Promise` for an array of `ILoanOrderActive` every item of which contains a unique hash representing the loan order, trader and expiration timestamp of the loan order

### getMarginLevels

Get current margin data for the loan order.

```typescript
  getMarginLevels(params: {
    loanOrderHash: string;
    trader: string;
  }): Promise<IMarginLevel>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.trader` the address of the trader in the loan order

**Returns**

`Promise` for `IMarginLevel` which represents current state of margin of the loan order

### getPositionOffset

Get the current profit/loss data of the position.

```typescript
  getPositionOffset(params: {
    loanOrderHash: string;
    trader: string;
  }): Promise<IProfitStatus>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.trader` the address of the trader in the loan order

**Returns**

`Promise` for `IProfitStatus` which represents current state of profits/losses of the loan order

### withdrawPosition

Allows the trader to withdraw any amount in excess of their loan principal. The trader will only be able to withdraw an amount the keeps the loan at or above initial margin.

```typescript
  withdrawPosition(params: {
    loanOrderHash: string;
    withdrawAmount: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.withdrawAmount` amount of the token to withdraw

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### depositPosition

Allows the trader to return the position/loan token to increase their escrowed balance. This should be used by the trader if they've withdraw an overcollateralized loan. If depositTokenAddress does not match the current position, it will traded with the oracle.

```typescript
  depositPosition(params: {
    loanOrderHash: string;
    depositTokenAddress: string;
    depositAmount: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.depositTokenAddress` the address of the token being returned

`params.depositAmount` amount of the token to deposit

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### getInterest

Get current interest data for the loan order.

```typescript
  getInterest(params: {
    loanOrderHash: string;
    trader: string;
  }): Promise<IInterestStatus>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.trader` the address of the trader in the loan order

**Returns**

`Promise` for `IInterestStatus` which represents current state of interests of the loan order

### payInterest

Pay the lender of a loan the total amount of interest accrued for a loan.

```typescript
  payInterest(params: {
    loanOrderHash: string;
    trader: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.trader` the address of the trader in the loan order

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### cancelLoanOrder

Cancels remaining \(untaken\) loan.

```typescript
  cancelLoanOrder(params: {
    order: ILoanOrderFillRequest;
    oracleData: any;
    cancelLoanTokenAmount: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.order` signed loan order `ILoanOrderFillRequest`

`params.oracleData` information specific for selected oracle. for bZxOracle it should be empty string, for augur oracle this should be augur oracle market.

`params.cancelLoanTokenAmount` the amount of remaining unloaned token to cancel

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### cancelLoanOrderWithHash

Cancels remaining \(untaken\) loan.

```typescript
  cancelLoanOrderWithHash(params: {
    loanOrderHash: string;
    cancelLoanTokenAmount: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.cancelLoanTokenAmount` the amount of remaining unloaned token to cancel

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### orderFilledAmount

Returns amount of tokens filled in the loan order

```typescript
  orderFilledAmount(
    loanOrderHash: string
  ): Promise<string>;
```

**Arguments**

`loanOrderHash` the collateral token used by the trader

**Returns**

`Promise` for `string` containing the cancelled amount

### orderCancelledAmount

Returns amount of tokens canceled in the loan order

```typescript
  orderCancelledAmount(
    loanOrderHash: string
  ): Promise<string>;
```

**Arguments**

`loanOrderHash` the collateral token used by the trader

**Returns**

`Promise` for `string` containing the cancelled amount

### closeLoanPartially

Called by the trader to close part of their loan early.

```typescript
  closeLoanPartially(params: {
    loanOrderHash: string;
    closeAmount: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.closeAmount` the amount of the loan token to return to the lender

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### closeLoan

Called by the trader to close their loan early.

```typescript
  closeLoan(params: {
    loanOrderHash: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### liquidateLoan

Checks that a position meets the conditions for liquidation, then closes the position and loan.

If called by `params.trader` himself, calls `closeLoan`.

```typescript
  liquidateLoan(params: {
    loanOrderHash: string;
    trader: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.trader` the trader of the position

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

## Collateral

### getInitialCollateralRequired

Calculates the initial collateral required to open the loan.

```typescript
  getInitialCollateralRequired(
    loanTokenAddress: string,
    collateralTokenAddress: string,
    oracleAddress: string,
    loanTokenAmountFilled: string,
    initialMarginAmount: string
  ): Promise<string>;
```

**Arguments**

`loanTokenAddress` the collateral token used by the trader

`collateralTokenAddress` desired address of the collateral the trader wants to use

`oracleAddress` the oracle address specified in the loan order

`loanTokenAmountFilled` the amount of loan token borrowed

`initialMarginAmount` the initial margin percentage amount \(i.e. 50 == 50%\)

**Returns**

`Promise` for `string` containing the minimum collateral requirement to open the loan

### changeCollateral

Change the collateral token being used for a loan.

This function will transfer in the initial margin requirement of the new token and the old token will be refunded to the trader.

```typescript
  changeCollateral(params: {
    loanOrderHash: string;
    collateralTokenFilled: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.collateralTokenFilled` the address of the collateral token used

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### depositCollateral

Increase the collateral for a loan. If depositTokenAddress does not match the current collateral token, it will traded with the oracle.

```typescript
  depositCollateral(params: {
    loanOrderHash: string;
    depositTokenAddress: string;
    depositAmount: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.depositTokenAddress` the address of the collateral token used

`params.depositAmount` the amount of additional collateral token to deposit.

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### withdrawCollateral

Allows the trader to withdraw excess collateral for a loan.

Excess collateral is any amount above the initial margin.

```typescript
  withdrawCollateral(params: {
    loanOrderHash: string;
    collateralTokenFilled: string;
    withdrawAmount: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.loanOrderHash` a unique hash representing the loan order

`params.collateralTokenFilled` the address of the collateral token used

`params.withdrawAmount` the amount of excess collateral token to withdraw

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

## Trade

### tradePositionWith0x

Execute a 0x trade using loaned funds.

```typescript
  tradePositionWith0x(params: {
    order0x: IZeroExTradeRequest;
    orderHashBZx: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.order0x` ZeroEx \(c\) trade order description

`params.orderHashBZx` a unique hash representing the loan order

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### tradePositionWith0xV2

Execute a 0x trade using loaned funds on 0x V2 protocol network.

```typescript
  tradePositionWith0xV2(params: {
    order0x: IZeroExV2TradeRequest;
    orderHashBZx: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.order0x` ZeroEx \(c\) trade order description

`params.orderHashBZx` a unique hash representing the loan order

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### tradePositionWithOracle

Execute a market order trade using the oracle contract specified in the loan referenced by `params.orderHash`.

```typescript
  tradePositionWithOracle(params: {
    orderHash: string;
    tradeTokenAddress: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.orderHash` a unique hash representing the loan order

`params.tradeTokenAddress` ERC20 contract address of the token to buy in the trade

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

## ETH/WETH

### wrapEth

Converts ETH to WETH Tokens.

```typescript
  wrapEth(params: {
    amount: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.amount` amount of ETH to convert to WETH

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### unwrapEth

Converts ETH to WETH Tokens.

```typescript
  unwrapEth(params: {
    amount: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.amount` amount of WETH to convert to ETH

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

## Utils

### generatePseudoRandomSalt

Static method that generates a pseudo-random UINT256 number.

```typescript
  static generatePseudoRandomSalt(): BigNumber;
```

**Arguments**

None

**Returns**

`BigNumber` instance

### getBalance

Get balance of specific ERC20 token at `params.ownerAddress`

```typescript
  getBalance(params: { tokenAddress: string; ownerAddress: string }): Promise<BigNumber>;
```

**Arguments**

`tokenAddress` address of token ERC20 contract `ownerAddress` address tokens' owner

**Returns**

`Promise` for `BigNumber` instance

### noop

Static method that does nothing. Just an empty function.

```typescript
  static noop(): void;
```

**Arguments**

None

**Returns**

Nothing

### requestFaucetToken

Request test token transfer `params.receiverAddress`.

```typescript
  requestFaucetToken(params: {
    tokenAddress: string;
    receiverAddress: string;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.tokenAddress` an address of token ERC20 contract

`params.receiverAddress` recipient wallet address

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

### toChecksumAddress

Will convert an upper or lowercase Ethereum address to a checksum address.

```typescript
  static toChecksumAddress(address: string): string;
```

**Arguments**

`address` an address string

**Returns**

The checksum address `string`

### transferToken

Transfers specified amount of tokens to `to` address.

```typescript
  transferToken(params: {
    tokenAddress: string;
    to: string;
    amount: BigNumber;
    getObject: boolean;
    txOpts: Tx;
  }): Promise<TransactionReceipt> | TransactionObject<TransactionReceipt>;
```

**Arguments**

`params.tokenAddress` an address of token ERC20 contract

`params.to` recipient wallet address

`params.amount` amount of tokens to transfer

`params.getObject` should this function return `Promise<TransactionReceipt>` \(`false`\) or `TransactionObject<TransactionReceipt>` \(`true`\)

`params.txOpts` web3 transaction options object \(`from`, `gasPrice`, `gas` etc.\)

**Returns**

`Promise<TransactionReceipt>` or `TransactionObject<TransactionReceipt>`

