---
comment:  Menu nesting required tbd todo HOWEVER this may apply to v2 as well as v1, if so it could jump up a level. Does this also apply to Partner API ?? Required because we could not throttle behaviour at the schema level for legacy vs EIP-1559 ETH tx [source== https://eips.ethereum.org/EIPS/eip-1559#gasprice] and [source== https://mycelium.xyz/research/the-journey-of-an-ethereum-transaction, but they have this "Forfeited if the cost exceeds this and the transaction is dropped."] ASKING FOR FEEDBACK https://qredo.atlassian.net/browse/BI-571 // wip final wording in swagger
title: Transactions
author: HB
proofedDate: 20221122
---

Within Qredo, a transaction is an instruction to a smart contract that may be signed (if the trusted parties approve it) and is returned ready to be broadcast.

{% callout type="note" title="Coming soon" %}
Expect sign functionalities e.g. EIP-712 and EIP-191 to be supported in Qredo API v1 in early 2023.
{% /callout %}

|Applies to|Prerequisites|
|----------|-------------|
|Qredo API v1| [Requirements](/api-reference/qredo-api) |


## Transaction overview

To broadcast a transaction request, it must be cryptographically signed. Thanks to Qredo's custody suite, only transactions that meet the conditions of the approval policy will be signed.

{% callout type="note" %}
    By using Qredo's MPC network to sign your transactions, you remove the single point of failure of a private key-based signature system. Futhermore, by having more than one approver in your policy, your custody rules remove the single point of failure that exists by allowing just one party to approve the signature being applied.
{% /callout %}

{% callout type="warning" title="The choice is yours" %}
While not recommended, you can choose to set up custody rules that require only one approval should you wish.
{% /callout %}


When you create transactions from the Web App or Partner interfaces, Qredo handles:

- the responsibility for collecting the required approvals
- signs the transaction
- **and** then returns the broadcast-ready transaction


### Lifecycle of a transaction

{% mermaid %}
graph TD
    A[1. Transaction request] -->|Signature request| B(2. Approvers)
    B --> C{3. Approval threshold}
    C -->|Signatures collected| D[4a. Signed broadcast-ready transaction]
    C -->|Insufficient approvals| E[4b. Unsuccessful request]
    D -->F[5. Broadcast transaction] 
    F-->G[6. Validator]
    G-->H[7. State change on chain] 
{% /mermaid %} 

If you are using [Qredo API v1](/api-reference/qredo-api), Qredo handles:

- the responsibility for collecting the required approvals
- signs the transaction
- returns it to you

Therefore, the API flow ends when the transaction is approved and signed and **ready to broadcast**, (as per 4a in the lifecycle of a transaction above).

## Supported Gas Types

Qredo API v1 supports [Type 0, 1](#legacy-transactions), and [2 (EIP-1559)](#type-2) transactions. 

Many digital assets are traded on [EVMs](https://chainlist.org/). Due to the [London hard fork](https://ethereum.org/en/history/#london), EVM transactions fall into two classes in terms of payment for fees:

- [EVM Type 0/1 "Legacy" transactions](#legacy-transactions)
- [EIP-1559 Type 2 transactions](#type-2)

Prior to the fork, `gasPrice` included the ETH paid per gas for the transaction *and* the ETH received by the miner. Within [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559), the concept of `gasPrice` evolved to be the ETH paid for the transaction.

This means that users forming a transaction request must determine whether they require an EIP-1559 transaction or a legacy transaction.

### Type 2

An EIP-1559 or Type 2 transaction allows the sender to state:

- how much gas they are willing to spend
- what proportion of that spend is the validator's "tip"
- how much they are willing to pay for that gas

```JSON
{
  to: "Address of recipient's Wallet.",
  gasLimit: "Maximum gas units the sender is willing to pay to have their transaction included.",
  maxFeePerGas: "Optional: Maximum amount the transaction initiator is willing to pay in ETH for each unit of gas. If passed, then `maxFeePerGas` must, at minimum, equate to the block's (base fee per gas + tip required) for the transaction to be included in the block.",
  maxPriorityFeePerGas: "The maximum fee (per each unit of gas) to be included as the validator's tip.",
  nonce: "Transaction nonce; a sequencially-incrementing counter which indicates the transaction number from the account.",
  value: "Amount of ETH to transfer to recipient address in gwei."
}
```
{% comment %} https://ethereum.org/en/developers/docs/gas/ {% /comment %}

The total fee, therefore, is the (units of gas used in the transaction) * (base fee per gas + `maxPriorityFeePerGas`). The base fee per gas is a property of the Ethereum protocol and varies to control spikes in demand. 

If the resultant total fee is less than the (`maxFeePerGas` * units consumed), then the difference is refunded. However, be aware that if the `gasLimit` that you set is too low for your transaction to succeed, the gas burned in attempting to complete that transaction up to that limit, is lost.


{% comment %}
The risk is, that should the base fee rise such that base fee + `maxPriorityFeePerGas` exceed maxFeePerGas, then `maxPriorityFeePerGas` is reduced insofar as is required to allow `maxPriorityFeePerGas` + base fee to remain within the maxFeePerGas threshold; thus reduceing the validator tip. 
{% /comment %}

### Legacy transactions 

Both Type 0 and Type 1 transactions allow the sender to state:

- how much gas they are willing to spend
- how much they are willing to pay for that gas

```JSON
{
  to: "Address of recipient's Wallet.",
  gasLimit: "Maximum gas units the sender is willing to pay to have their transaction included."
  gasPrice: "The maximum amount of ETH that each unit of gas costs for the transaction and mining fees."
  nonce: "Transaction nonce; a sequencially-incrementing counter which indicates the transaction number from the account.",
  value: "Amount of ETH to transfer to recipient address in gwei."
}
```

{% comment %} 
https://ethereum.org/en/developers/docs/gas/ Docs required interpretation ! *Total fee would have been: Gas units (limit) * Gas price per unit i.e 21,000 * 200 = 4,200,000 gwei or 0.0042 ETH* It might be that the tx simply paid the upper amount, rather than having the opportunity to enjoy cheaper txs i.e., gasLimit always applied, not a conditional?? Does anyone know about the bad old days of 2021/pre London fork? -- was it really a "guess the base gas price and have a go and pay your guess"?
{% /comment %}

The total fee is the (units of gas consumed * network gas price). The network gas price is a variable property that depends upon the Ethereum network itself. 

