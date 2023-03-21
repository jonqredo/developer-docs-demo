---
title: Setup Core Client
comment: Core client offering deprecated, need logs to monitor who is still using this service.
author: TK
proofedDate: noProof
hide:
  - navigation
---

{% callout type="warning" title="Signing Agent replaces Core Client" %}
Core Client v2 is now called Signing Agent.
{% /callout %}

{% callout type="note" title="Core Client (Signing Agent v1) is deprecated" %}
As of Q4 2022, Signing Agent v2 replaces v1: Core Client. Please create new integrations with [Signing Agent](/developer-guides/signing-agent).
{% /callout %}

This section presents the Qredo Core Client and illustrates its intended use for automated custody.

The Core Client is a standalone approver service. Every Core Client has a unique ID and uses a dedicated subset of the API to perform its functions. You can plug in with your client ID to a WebSocket feed that listens for transactions pending approvals. Also, you can create a programmable approver bot that uses a Core Client instance to perform automated custody.

This topic covers the API custody on transfer out transactions. The Core Client must be appointed as an approver of a wallet (or fund). You will learn how to fetch, approve or reject pending transactions out of such a wallet. A Core Client instance can be upgraded to an *exchange*, as described in the [Get started with exchanges](/developer-guides/partner-api/exchanges) section.

{% callout type="note" title="The Core Client performs custody over *transfer out* transactions" %}

The Core Client acts just like a human approver, which means it *approves* or *rejects* all transfer out transactions:

* transfer -  a L2 to L2 transaction where assets in one Qredo wallet move to another Qredo wallet. 
* withdrawal - a L2 to L1 transaction where assets in a Qredo wallet move to a wallet outside the Qredochain (on BTC, ETH, etc.).
* atomic swap - a two-way L2 to L2 transaction where you offer a certain amount of an asset (transfer out) in exchange for a certain amount of another asset. (e.g. exchange 100000000 ETH qweis for 735601 satoshis). Both parties that participate have one transfer in transaction; and one transfer out transaction that undergoes custody with their approvers. This transaction type is discussed in more detail in the [Atomic swaps](/developer-guides/partner-api/atomic-swaps) section.

{% /callout %}

To complete this section, you must:

1. [Create](#1-set-up-a-core-client) a Core Client using the `POST /coreclient` Partner API resource.
2. Add the Core Client as a trusted party of a company. You will use the `POST /trustedparty` to add the Core Client to the trusted network of your company, similar to [Partner API quick start Step 2](/developer-guides/partner-api/walkthrough/02-add-trusted-party).
3. Create a wallet (or a fund, as explained in the Partner API quick start walkthrough step [3. Create a fund](/developer-guides/partner-api/walkthrough/03-create-fund)) with the Core Client as a trusted party.

As you see, the Core Client undergoes a process very similar to that of the human approver.

## 1. Create a Core Client

This step discusses the addition of a single Core Client but you can use the same API resource to add as many as you want to have.

### Example request: `POST /coreclient`

This example creates a `coreclient` named "Approver-Bot-01". The response returns the client `id` and the WebSocket `feed`. Note that you will need the `id` as a query parameter with custodial API endpoints.

```bash
curl -X POST "https://api.qredo.network/api/v1/p/coreclient"
```

#### Request body

Specify the `name` of your Core Client. This name is for human-readable info only: you identify your Core Client in the API using its only ID.

```json
{
  "name": "Approver-Bot-01"
}
```

### Example response: `POST /coreclient`

Upon success, the response notably returns the core `id` and the unique WebSocket `feed`.

```json
{
  "id": "9niR8...RW3xu",
  "name": "Approver-Bot-01",
  "feed": "wss://api.qredo.network/api/v1/p/coreclient/9niR8...RW3xu/feed",
  "timestamp": "<Epoch timestamp when Core Client is created, e.g. '1635258443' >"
}
```

The example includes the suggested `id` and `feed`.

{% callout type="note" title="Use the `id` and `feed` URL to connect your WebSocket and perform custody" %}
* You identify your Core Client by its `id` to fetch pending transactions or perform custody. This is the same as the `client_id` with most API calls that are part of the `/coreclient` endpoint.
* Save the `feed` URL to connect the Core Client.
{% /callout %}

## 2. Enlist the Core Client as a trusted party

You must follow a process very similar to [Step 2: Add a trusted party](/developer-guides/partner-api/walkthrough/02-add-trusted-party) of the [Partner API quick start](/developer-guides/partner-api/get-started) walkthrough.

Again, you will use the `POST /trustedparty` API resource. The difference here is that, this time, you will not submit a person's email address in the API call body, but the core `id` instead.

### Example request: `POST /trustedparty`

This example submits an *enrollment request* to add as an approver the Core Client with the `id` retrieved in the previous step. With this API resource, you pass [`company_id`](/developer-guides/partner-api/walkthrough/01-create-company) as a URL parameter.

```bash
curl -X POST "https://api.qredo.network/api/v1/p/company/{company_id}/trustedparty"
```

#### Request body

Unlike when adding a human approver, you obviously don't specify Core Client by an email address but with the Core Client `id`.

```json
{"address": "9niR8...RW3xu"}
```

### Example response: `POST /trustedparty`

A successful response returns a `HTTP 200 OK` confirmation. The appointed user will receive a notification on their Qredo Signing app. No JSON payload is passed with the response.

Unlike when adding a human user account, no confirmation is required and this step is already completed.

## 3. Verify enlisting of your Core Client approver

You can verify that your Core Client can actively participate in the approval process using the familiar  `GET /trustedparty` API resource to [return all trusted parties](/developer-guides/partner-api/walkthrough/02-add-trusted-party) with your company.

{% callout type="note" title="The response returns core `id` as a `trusted_entity_id`" %}
The example response includes the human Qredo account approver as `"type":"user"`. The Core Client is represented as `"type":"core-client"` and its core `id` is the value for the `trusted_entity_id` and `address` parameters.
{% /callout %}

```json
{
  "company_id": "1zga...gVBl",
  "total_count": 2,
  "list": [
    {
      "trusted_entity_id": "13DCS...vDiag",
      "name": "John Smith",
      "address": "custodian@qredouser.com",
      "type": "user",
      "created": 1634727142
    },
    {
      "trusted_entity_id": "9niR8...RW3xu",
      "name": "Demo Core Client",
      "address": "9niR8...RW3xu",
      "type": "core-client",
      "created": 1635427044
    }
  ]
}   
```

## 4. Create a wallet with the Core Client as a trusted party

You can create a new fund (with one or more wallets) and appoint the Core Client as a trusted party member with global custodial policies. This example, however, creates a new wallet, part of an existing fund. The custodial policies of the new wallet appoint the Core Client as member, using its client `id`.

### Example request: `POST /wallet`

This examples creates a ETH-Testnet wallet with custom custodial policies with the Core Client added as a `member`.  With this API resource, you pass [`company_id`](/developer-guides/partner-api/walkthrough/01-create-company) and [`fund_id`](/developer-guides/partner-api/walkthrough/03-create-fund) as URL parameters.

#### Request body

Specify the wallet name and asset, as well as the custodial policies. Each custodial policy in this example includes a core `id` ___and___ a `trusted_party_id`, with a `threshold` set to `1`. In this way a single Core Client can approve transfers and withdrawals with this wallet.

{% callout type="note" title="This example adds a human approver as well" %}
To reduce the risk of getting your assets "stuck" in your wallet, Qredo recommends that you also appoint the necessary (threshold) number of human approvers (``"type": "user"``). Remember that a transaction on the Qredochain cannot be processed without undergoing custody.
{% /callout %}

The current example creates a single wallet. You can create more than one wallets with a single API call.

```json
{
  "wallets": [
    {
      "name": "Core Client custody wallet",
      "asset": "ETH-TESTNET",
      "custodygroup_withdraw": {
        "threshold": 1,
        "members": [
          "9niR8...RW3xu",
          "13DCS...vDiag"
        ]
      },
      "custodygroup_tx": {
        "threshold": 1,
        "members": [
          "9niR8...RW3xu",
          "13DCS...vDiag"
        ]
      }
    }
  ]
}
```

### Example response: `POST /wallet`

The response returns a list of IDs of the wallets created. 

```json
{
  "wallets": [    
    "GTci7...tPc7i"
  ]
}
```

{% callout type="warning" title="Wallet ID vs wallet address" %}
Wallet ID is pertinent to Qredo network only. Do not mistake this ID with the wallet address. In terms of Qredo, a wallet address is used only when moving assets from L1 blockchain to Qredochain (deposits), or vice versa: from Qredochain to L1 blockchain (withdrawals). Only *deposit wallets* have addresses. In all API calls for moving assets within the Qredo Network, or query wallet details, use this `wallet_id` parameter.
{% /callout %}

From this point on, you need to deposit assets to your wallets, as described in step [4. Perform a deposit](/developer-guides/partner-api/walkthrough/04-perform-deposit) of the Partner API quick start walkthrough.

Next, you [connect](connect-core-client) the Core Client to a WebSocket feed service and start [listening for transaction requests](approve-core-client) pending custody.
