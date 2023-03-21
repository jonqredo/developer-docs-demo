---
comment:
author: TK
title: Atomic swaps
---

## Overview

Qredo users can exchange assets via atomic swaps. Atomic swaps allow two parties to trade cryptoassets that reside on different blockchains, e.g. exchange some amount of BTC for some amount of ETH.

Therefore, the party initiating the atomic swap, proposes a quote and can post it to a liquidity hub or to a dedicated receiver. The initiator party that creates the swap quote is called the liquidity Maker. The party that accepts a quote is the Taker. To learn more, see [Atomic swap roles](/developer-guides/partner-api/atomic-swaps#atomic-swap-roles).

To complete this section, you go through the flows of Maker and Taker:

1. As Maker, create an atomic swap quote using `POST atomicswap/make`.
2. Have Maker's approvers approve the quote.
3. As Maker, submit the quote to the Liquidity Hub using `POST /liquidityhub`. Optionally, you can use an external secure channel to pass directly a quote to another entity (must be part of the same company).
4. As a potential Taker, retrieve quotes on the Liquidity Hub using `GET /liquidityhub` or a WebSocket feed.
5. As Taker, select a quote by `tx_id` to take it using `POST /atomicswap/take/{tx_id}`.
6. Have Taker's approvers approve the quote.

This is the entire flow of an atomic swap and the swap completes in a single transaction.

{% callout type="note" title="Taker and Maker must have Wallets of both asset types" %}
Each side must have Wallets of both asset types, e.g. ETH & BTC. Obviously, the sending Wallets of both Maker and Taker must have the necessary balance to complete an atomic swap.
{% /callout %}

## Atomic swap roles

Completion of an atomic swap requires a Maker to create and submit the quote and the Taker to retrieve and take the quote.

**Maker**

If the quote offers BTC for ETH, it would be in the form of (for example): I offer you 7550075 BTC satoshis in exchange for 1000000000 gweis worth of ETH. Obviously, this requires the Maker to have Wallets with both asset types in the quote: a "sending" Wallet with BTC and a "receiving" Wallet ETH.
Once you submit the atomic swap, it undergoes custody as it counts as an outgoing tx. The quote is ready to be posted to liquidity hub. Optionally, you can share the swap quote `tx_id` with a *Taker* Qredo user via a secure channel, and that Taker can take it from here.

**Taker**

Once a quote is published, you can view it on the liquidity hub. Optionally, if the Maker has shared with you the quote, you can skip the liquidity hub. Then, select a quote using its `tx_id` and accept it. Your transaction must undergo custody before you complete the swap. Obviously, this requires the Taker to have Wallets with both asset types in the quote: but to be able to accept the quote (sell ETH for BTC as the Maker's quote offers), the "sending" Wallet must have ETH and the "receiving" Wallet must be of type BTC.

## 1. Maker: Create atomic swap quote

With atomic swaps a company (identified by `company_id`) submits a quote to a "partner Wallet".

* Select the origin (`send_wallet_id`) and a the destination (`receive_wallet_id`).
* Specify the assets and quantities for sending and receiving.
* Set an expiry time for the swap.
* Post the details to the endpoint.

### Example request: `POST atomicswap/make`

```bash
curl -X POST "https://api.qredo.network/api/v1/p/company/{company_id}/atomicswap/make" 
```

### Request body

To make this example more specific, Maker will offers 755006 satoshis worth of BTC in exchange for 100000000 gweis worth of ETH.

```json
{
  "send_wallet_id": "<Wallet ID for asset to send: not the address>",
  "receive_wallet_id": "<Wallet ID for asset to receive: not the address>",
  "expires": "<Epoch timestamp when atomic swap quote expires if not approved>",
  "reference": "<reference value: a custom field>",
  "partner_txID": "<a transaction ID: a custom field>",
  "send": {
    "symbol": "BTC",
    "amount": 755006
  },
  "receive": {
    "symbol": "ETH",
    "amount": 100000000
  },
  "add_to_liquidity_hub": false
}
```

When `add_to_liquidity_hub` set to `true`, the swap will be published to the Liquidity Hub once it gets approved.

{% comment %} 
???+ info "Wallet IDs are interchangeable with Wallet *short codes*"
    Notice that Wallets on the Qredo Network have `short_code` values: a combination of three unique English words separated by space intervals, in the format, e.g. `"send_wallet_id": "firstword secondword thirdword"`. To see the short code of a Wallet, call `GET wallet/{wallet_ID}` and see the Wallet `short_code` value in the response.
{% /comment %} 

### Example response: `POST /atomicswap/make`

The response notably returns the swap quote `tx_id`.

```json
{
  "tx_id": "bda3da......eae4e80",
  "tx_url": "https://qredo.network/bda3da......***eae4e80",
  "status": "new"
}
```

## 2. Undergo custody on Maker's side

As a Maker, your quote must undergo custody on the outgoing transaction, which specifically concerns the *send Wallet*. In case you're using a Core client, it will receive the pending action:

```json
{
  "coreClientID": "9niR8...RW3xu",
  "expireTime": 1641998800,
  "id": "<the action_id, not the same as the tx_id>",
  "status": "pending",
  "timestamp": "<Epoch timestamp when atomic swap is submitted by Maker>",
  "type": "ApproveSwap"
}
```

If the Taker is using a Core Client for custody, the feed will present the tx as `"type": "ApproveSwap"` and an action `id`.

Similarly to all submitted transactions pending custody, use `POST ` Once an approval is granted to the quote, you can move on.

## 3. Maker: Post to liquidity hub

This step is optional since you are not obliged to post to the liquidity hub. Currently, atomic swaps are available to a single API account only. This means that both Maker and Taker must belong to the same API account. Maker and Taker may belong to different companies. Learn more about the [Liquidity Hub](https://qredo.zendesk.com/hc/en-us/articles/4406883003793-What-Is-the-Liquidity-Hub-) on the Qredo Help Center.

* Add a quote to the liquidity hub with the `tx_id` from the previous step.
* Use the maker's `company_id`.

### Example request: `POST /liquidityhub`

```curl
curl -X POST "https://api.qredo.network/api/v1/p/company/{company_id}/liquidityhub" 
```

#### Request body

```json
{
  "tx_id": "1lm519eiGT......HrEYvSBuSn"
}
```

### Example response: `POST /liquidityhub`

```bash
HTTP 200 OK
```

## 4. Taker: Retrieve all current swap quotes

Practically, to get all available quotes on the liquidity hub, the Taker could use either (or both):

* the `GET /liquidityhub` API resource to retrieve all currently available quotes.
* the liquidityhub WebSocket to retrieve all newly published quotes.

In both cases you get the `tx_id` you need to move onwards.

### Example request: `GET /liquidityhub`

This example retrieves all quotes posted on liquidity hub. With this API resource, you pass your Core `company_id` as a URL parameter.

```bash
curl -X GET "https://api.qredo.network/api/v1/p/company/{company_id}/liquidityhub" 
```

{% comment %} #### WHAT is that supposed to mean: The `company_id` returns atomic swaps associated with that company - indicated in the `owner` field of the response.{% /comment %}

### Example response: `GET /liquidityhub`

The response returns a list of available atomic swaps.

``` json
{
  "items": [
    {
    "tx_id": "1lmBA...doZYO,",
    "send_asset": "BTC",
    "send_amount": 755006,
    "receive_asset": "ETH",
    "receive_amount": 100000000,
    "owner": "<a flag indicating if atomic swap belongs to queried company_id:'true' or 'false'>",
    "expires": "<Epoch timestamp when atomic swap quote expires if not approved>",
    "added": "<Epoch timestamp when atomic swap quote added to LH>",
    }    
  ]
}
```

### Optional: Use Liquidity Hub WebSocket feed
  
As an example, you can use the [Signing Client](/developer-guides/partner-api/sign-api-calls#partnerapi-signing-client) to connect to a liquidityhub WebSocket feed.

{% tabs %}
{% tab label="Mac/Linux" %}
1. Launch the CLI and navigate to the directory where the Signing Client is located.
2. In the CLI, type the following: 
```
   ./partnerapi-sign liquidityhub
```
4. The prompt will ask you to enter the WebSocket URL. Paste in the following link:
```bash
wss://api.qredo.network/api/v1/p/liquidityhub/feed
```
{% /tab %}
{% tab label="Windows" %}
1. Launch `cmd.exe` and navigate to the directory where the Signing Client is located.
2. In the `cmd` interface, type the following: 
```
partnerapi-sign.exe liquidityhub
```
3. The prompt will ask you to enter the WebSocket URL. Paste in the following link:
```bash
wss://api.qredo.network/api/v1/p/liquidityhub/feed
```
{% /tab %}
{% /tabs %}

Upon success, the CLI will print out `connected`.

{% callout type="note" title="Newly posted swaps in `liquidityhub` feed" %}
Whenever a swap quote is posted to Liquidity Hub, the WebSocket feed prints out the `Tx_ID` and its current `Status`:    
```
UPDATE IN LIQUIDITY HUB. TxID: 1lmBA...doZYO, Status: taker_pending
```
{% /callout %}


## 5. Taker: Accept the quote

As the Taker, you can use the transaction ID `tx_id` returned in the `GET /liquidityhub` response to get more details about the quote using the `GET /atomicswap/make/{tx_id}` API resource.

```curl
curl -X GET "https://api.qredo.network/api/v1/p/company/{company_id}/atomicswap/make/{tx_id}"
```

* Select a fund with the required Wallets of the required assets. Obviously, your send Wallet must contain the required balance to complete the swap.
* Complete the swap:

```
curl -X POST "https://api.qredo.network/api/v1/p/company/{company_id}/atomicswap/take/" 
```

* Use `fund_id`, `swap_id` and `reference` to take the quote before it expires.

### Example request: `POST /atomicswap/take/{tx_id}`

```bash
curl -X POST "https://api.qredo.network/api/v1/p/company/{company_id}/atomicswap/take" 
```

### Request body

```json
{
  "swap_id": "<the tx_id returned using GET /liquidityhub>",
  "send_wallet_id": "<Wallet ID for asset to send: not the address>",
  "receive_wallet_id": "<Wallet ID for asset to receive: not the address>",
  "reference": "CX15R99XX",
  "partner_txID": ""
}
```

### Example response: `POST /atomicswap/take/{tx_id}`

A successful response returns a new `tx_id`. The newly submitted transfer out transaction must now undergo custody.

{% callout type="note" title="Do not mistake the newly returned tx_id for action `id`." %}
The newly created `tx_id` is **not** the same as the action `id` that the trusted party will receive for custody!
{% /callout %}

You can use this `tx_id` to query this particular transaction, using `GET /company/{company_id}}/atomicswap/make/{tx_id}`.

## 6. Undergo custody on Taker's side

Taking an atomic swap needs approval on Taker's side, using the new `tx_id`. Only after this approval is the swap completed.

If the Taker is using a Core Client for custody, the feed will present the tx as `"type": "ApproveSwapTake"` and an action `id` (not the same as the `tx_id` returned in the previous step):

```json
{  
  "coreClientID": "6kC7C...Vuz4W",
  "expireTime": "<Epoch timestamp when atomic swap quote expires if not approved>",
  "id": "22NV0...GYd5",
  "status": "pending",
  "timestamp": "<Epoch timestamp when atomic swap is accepted by Taker>",
  "type": "ApproveSwapTake"
}  
```

Use `PUT /company/{company_id}}/action/{action_id}` for approval of Taker's transaction. A successful response will return `HTTP 200 OK`.

{% callout type="note" title="View tx status and company holdings to see how your balance is reflected" %}

With the example described, the Maker's receive Wallet balance should increase with 100000000 gweis worth of ETH.

On the Taker's side, their BTC Wallet is the receiver one, and its balance should increase with 755006 satoshis worth of BTC.

* View transaction status using the `GET /transfer/{tx_id}` API resource, as explained in Partner API quick start walkthrough Step 5. [Perform a transfer](/developer-guides/partner-api/walkthrough/05-perform-transfer#3-view-transaction-status).

* Retrieve company holdings to view how your balance is reflected.
```bash
GET https://api.qredo.network/api/v1/p/company/{company_id}/holding
```
{% /callout %}

