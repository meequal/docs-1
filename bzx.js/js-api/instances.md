# Instances

#### Constructor

Creates an instance of BZxJS.

```typescript
  constructor(
    web3: Web3,
    params: { networkId: number; addresses?: string[]; }
  );
```

**Arguments**

`web3` web3 instance

`params.networkid` id of the network to connect \(for example `3` for `ropsten`\)

`params.addresses` a map containing the bZx contracts addresses in the specified network

**Returns**

`BZxJS` instance

