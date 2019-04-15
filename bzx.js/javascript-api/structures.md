# Structures

## ITokenDescription

```typescript
export declare interface ITokenDescription {
  address: string;
  name: string;
  symbol: string;
  decimals: number;
  url: string;
}
```

## IOracleDescription

```typescript
export declare interface IOracleDescription {
  address: string;
  name: string;
}
```

## ILoanOrderActive

```typescript
export declare interface ILoanOrderActive {
  loanOrderHash: string;
  trader: string;
  loanEndUnixTimestampSec: number;
}
```

## ILoanOrderFillable

```typescript
export declare interface ILoanOrderFillable {
  makerAddress: string;
  takerAddress: string;
  tradeTokenToFillAddress: string;
  withdrawOnOpen: boolean;
  loanTokenAddress: string;
  interestTokenAddress: string;
  collateralTokenAddress: string;
  feeRecipientAddress: string;
  oracleAddress: string;
  loanTokenAmount: number | string;
  interestAmount: number | string;
  initialMarginAmount: number | string;
  maintenanceMarginAmount: number | string;
  lenderRelayFee: number | string;
  traderRelayFee: number | string;
  maxDurationUnixTimestampSec: number | string;
  expirationUnixTimestampSec: number | string;
  loanOrderHash: string;
  lender: string;
  orderFilledAmount: number;
  orderCancelledAmount: number;
  orderTraderCount: number;
  addedUnixTimestampSec: number;
}
```

## ILoanOrderFillRequest

```typescript
export declare interface ILoanOrderFillRequest {
  makerAddress: string;
  takerAddress: string;
  tradeTokenToFillAddress: string;
  withdrawOnOpen: boolean;
  loanTokenAddress: string;
  interestTokenAddress: string;
  collateralTokenAddress: string;
  feeRecipientAddress: string;
  oracleAddress: string;
  loanTokenAmount: number | string;
  interestAmount: number | string;
  initialMarginAmount: number | string;
  maintenanceMarginAmount: number | string;
  lenderRelayFee: number | string;
  traderRelayFee: number | string;
  maxDurationUnixTimestampSec: number | string;
  expirationUnixTimestampSec: number | string;
  bZxAddress: string;
  makerRole: number;
  salt: string;
  signature: string;
}
```

## ILoanPositionState

```typescript
export declare interface ILoanPositionState {
  lender: string;
  trader: string;
  loanOrderHash: string;
  loanStartUnixTimestampSec: number;
  loanEndUnixTimestampSec: number;
  active: number;
  loanTokenAddress: string;
  loanTokenAmountFilled: number;
  collateralTokenAddressFilled: string;
  collateralTokenAmountFilled: number;
  positionTokenAddressFilled: number;
  positionTokenAmountFilled: number;
  interestTokenAddress: string;
  interestTotalAccrued: number;
  interestLastPaidDate: number;
  interestPaidSoFar: number;
}
```

## IZeroExOrder \(obsolete, use `IZeroExV2Order`\)

```typescript
export declare interface IZeroExOrder {
  exchangeContractAddress: string;
  expirationUnixTimestampSec: number;
  feeRecipient: string;
  maker: string;
  makerFee: number;
  makerTokenAddress: string;
  makerTokenAmount: number;
  salt: string;
  taker: string;
  takerFee: number;
  takerTokenAddress: string;
  takerTokenAmount: number;
}
```

## IZeroExV2Order

```typescript
export declare interface IZeroExV2Order {
  senderAddress: string;
  makerAddress: string;
  takerAddress: string;
  makerFee: string;
  takerFee: string;
  makerAssetAmount: string;
  takerAssetAmount: string;
  makerAssetData: any;
  takerAssetData: any;
  salt: string;
  exchangeAddress: string;
  feeRecipientAddress: string;
  expirationTimeSeconds: string;
}
```

## ITokenMetadata

```typescript
export declare interface ITokenMetadata {
  name: string;
  symbol: string;
  decimals: number;
}
```

## IZeroExV2OrderMetadata

```typescript
export declare interface IZeroExV2OrderMetadata {
  makerToken: ITokenMetadata;
  takerToken: ITokenMetadata;
}
```

## ISignatureParams

```typescript
export declare interface ISignatureParams {
  v: number;
  r: Buffer;
  s: Buffer;
}
```

## IZeroExOrderSigned \(obsolete, use `IZeroExV2OrderSigned`\)

```typescript
export declare interface IZeroExOrderSigned extends IZeroExOrder {
  ecSignature: ISignatureParams;
}
```

## IZeroExV2OrderSigned

```typescript
export declare interface IZeroExV2OrderSigned extends IZeroExV2Order {
  signature: string;
}
```

## IZeroExTradeRequest \(obsolete, use `IZeroExV2TradeRequest`\)

```typescript
export declare interface IZeroExTradeRequest {
  signedOrder: IZeroExOrderSigned;
}
```

## IZeroExV2TradeRequest

```typescript
export declare interface IZeroExV2TradeRequest {
  signedOrder: IZeroExV2OrderSigned;
  metadata: IZeroExV2OrderMetadata;
}
```

## IConversionData

```typescript
export declare interface IConversionData {
  rate: string;
  amount: string;
}
```

## IMarginLevel

```typescript
export declare interface IMarginLevel {
  initialMarginAmount: string;
  maintenanceMarginAmount: string;
  currentMarginAmount: string;
}
```

## IInterestStatus

```typescript
export declare interface IInterestStatus {
  lender: string;
  interestTokenAddress: string;
  interestTotalAccrued: string;
  interestPaidSoFar: string;
}
```

## IProfitStatus

```typescript
export declare interface IProfitStatus {
  isPositive: boolean;
  offsetAmount: string;
  positionTokenAddress: string;
}
```

