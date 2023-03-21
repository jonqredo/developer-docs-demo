---
comment: Core client offering deprecated, probably exchange offering as well consider editing/moving/removing this section.
author: TK
title: Exchanges
---

## Get Started

This section introduces a basic test flow to set up and connect a Core Client to perform exchange duties. On the Qredo network, an exchange is represented by its own Qredo user account with Partner API access and a Core Client that is authorized to perform exchange tasks.

{% comment %} <<< Speak with John{% /comment %}

Integration with Qredo would allow a third-party service (like a crypto exchange) to offer Qredo custodial Wallets to its user base. Note that an exchange is often referred to as a "counterparty" because it is basically a special type of custodian (approver) on the Qredo network and participates in approving transactions of their own users who have Qredo accounts (and specific Wallets).

### Exchange integration

From an Exchange standpoint, Qredo integration applies to exchange end users who also have Qredo user accounts. These Qredo end users have the option to *connect* one or more of their Qredo Wallets to that exchange. This allows an exchange to link these "connected" Qredo Wallets to their exchange accounts. Such Wallets must be of type *dedicated counterparty* or *Multi-Counterparty*. A dedicated Wallet connects with a single exchange while the Multi-Counterparty can connect with several.

{% comment %} Add a link when time comes instead of "more on that later" above{% /comment %}

{% callout type="note" title="Exchange Wallet types: dedicated vs Multi-Counterparty" %}

**Dedicated counterparty Wallet** - a type of Qredo Wallet that connects to a single counterparty (exchange). The end user of the Qredo web app creates a *dedicated Wallet* and selects the *counteparty* (exchange) from a list. Transactions out of dedicated Wallets undergo custody as ever but this time require an exchange approval as well. In other words: every transaction out of a connected Wallet undergoes custody, according to the applicable policy; and additionally requires an approval by the exchange. In this way, the Exchange Core Client acts as a *master custodian* with custodial control over assets in the connected dedicated Wallets. This is useful, for example, when the end user may have a limit order open on the exchange side. The exchange must ensure the respective account holds enough available crypto to meet the limit order and this is accomplished with custody. If a user attempts to move assets out of the Wallet in a way that the remaining balance would be insufficient to fulfil any open positions on the exchange, the exchange can block this transaction. Exchanges are also granted “sweep” rights over the Wallet to allow them to fulfil orders without further approval from the user or the user’s approvers.

**Multi-counterparty Wallet** - a type of Qredo Wallet that connects to one or more counterparties. Unlike a dedicated Wallet, the end user of the Qredo web app doesn't have to select counterparties during Wallet creation. Another difference is that the user can connect exchanges to this Wallet at any point after creation. The purpose of this Wallet is to enable a trader to have multiple market orders placed on different exchanges. This is a non-leveraged Wallet and has the same features as a standard Wallet. A third party can perform sweep transactions with this Wallet, bypassing Wallet approvers. Unlike the dedicated Wallet, with Multi-Counterparty Wallet, the exchange doesn't apply additional custody to outbound transfers.

{% /callout %}

{% comment %} 
should it be "fill" instead of 'fulfil'? 

(https://qredo.zendesk.com/hc/en-us/articles/4405878203921-Dedicated-Wallet))
(https://qredo.zendesk.com/hc/en-us/articles/4405878235025-Multi-Counterparty-Wallet))

Can they remove exchanges? -  Probably not. 
{% /comment %}

### Exchange Core Client duties

The exchange on Qredo is essentially a type of a Core Client that performs the following functions:

* approve or reject requests to *connect* Qredo Wallets to the exchange.
* approve or reject *sweep transactions* initiated by exchanges from any connected Wallets.
* act as a master custodian for connected *dedicated Wallets*.

Basically, using the Exchange Core Client is similar to the [regular custodial Core Client](/developer-guides/signing-agent/v1/set-up-core-client). Again, you can connect to the WebSocket feed. You identify your exchange client using its `id` and use the same Core Client API resources for approvals and rejections. However, there are a few additional API resources, e.g. for performing sweep transactions.

{% comment %} Add links above when time comes //The Exchange Core Client // {% /comment %} 

This section will guide you through the process of setting up your exchange and test out its functionality.

### End user's perspective

The Qredo web app end user creates a Wallet of type *dedicated* or *Multi-Counterparty*. With a dedicated Wallet, they select a *counterparty*, which is the exchange(s) to connect your Wallets to.

This section will guide you through the end user process and the respective interaction with the exchange side.

{% comment %} Confirm sentence 2 in note: {% /comment %}

{% callout type="note" title="This example will use a dedicated Wallet" %}
A Wallet of type *dedicated* will be used in this example. The Multi-Counterparty Wallet experience is similar from an exchange standpoint.
{% /callout %}

## Overview of steps

In the suggested flow, steps 1 to 3 help you set up your exchange and steps 4 onward present a basic test flow with end user interaction.

1. Create a *Core Client* as described in [create a Core Client](/developer-guides/signing-agent/v1/set-up-core-client). You will be using it to get your exchange feed.
2. Set up branding and test accounts: contact Qredo and provide your Exchange Core Client `id`, plus your exchange name and logo, as well as user metadata and test accounts.
3. Connect your Exchange Core Client to a [WebSocket](/developer-guides/signing-agent/v1/connect-core-client) and start listening for client activity.
4. Access the Qredo Web app with a test end user account (from the list provided in step 3) in the Qredo Web app, set up a fund (if you haven't done so already) and create a *dedicated* Wallet to connect.
5. On the Exchange Core Client, approve the Wallet connect request.
6. From end user account in the Qredo Web app, confirm the Wallet is activated.
7. Perform tests of available actions and the WebSocket feed with the Exchange Core Client.

After you verify that everything described in these steps works as expected, you can proceed to go live with your exchange.

### 1. Create a Core Client instance

Follow the instructions in the [Create and set up a Core Client](/developer-guides/signing-agent/v1/set-up-core-client) section to create your Exchange Core Client.

### 2. Set up branding and test accounts

The branding setup includes all the visual aspects of your exchange in the Qredo web app. Before you go live, send the following data to your Qredo account manager:

1. `id`: the id of the Core Client created in the previous step.
2. **Name** and **logo** of your exchange: this is how Qredo users will identify your exchange in the Qredo web app.
3. User **metadata** that is needed for connecting Qredo users to the exchange. Currently Qredo supports a *customer ID* (a unique ID provided to the user by you as an exchange) which can optionally be combined with email address (the address used by the user to register on your exchange platform). The end user will have to supply this metadata each time they create a dedicated or Multi-Counterparty Wallet in the Qredo web app.
4. Create one or a few Qredo test accounts. Note that your exchange will become available in the Qredo web app after it goes live. Before you're at that stage, you can use test accounts: only they will be able to visualize your exchange in the Qredo web app. Create and register your Qredo user test accounts and provide the Qredo sign-in email addresses.

After you are finalize these steps, send all data to your Qredo account manager so you can move on with the steps that follow.

### 3. Connect Exchange Core Client to feed

Use your Exchange Core Client `id` and follow the instructions to [connect the Core Client to the WebSocket feed](/developer-guides/signing-agent/v1/connect-core-client) and start listening for client activity.

The exchange feed follows a format similar to that of the approver Core Client feed.

{% callout type="note" title="Your exchange is fully operational now" %}
Once you have successfully completed Step 3 of this tutorial, your exchange is fully functional to perform the relevant operations.
{% /callout %}

The remaining steps help you test out your exchange alongside the end user experience in the Qredo web app.

### 4. End user: create a dedicated Wallet

In this step, the Qredo user creates a Wallet in Qredo Web app of either type: dedicated counterparty.

Use your test account as prepared and provided to Qredo in [step 2](#2-set-up-branding-and-test-accounts) and perform the following:

1. Sign in to the Qredo web app.
2. Create a [fund](https://qredo.zendesk.com/hc/en-us/articles/4404077381265-Creating-a-Fund-Setting-Up-Your-Wallet)(if you don't want to use an existing one) and set up a Wallet of type *dedicated counterparty*. If you already have a fund you want to use, simply add a dedicated Wallet.
3. During process of setting up the Wallet, select your exchange from the **Counterparty** drop-down list.
4. The process will require the end user to enter the required metadata (that the exchange has communicated with the Qredo account manager).
5. Once The Wallet appears as **Pending** in the Qredo Web app for that account.

{% comment %}Why a fund in step 2???{% /comment %}

Upon completion of this procedure, the Qredo web app sends the exchange a request to connect this dedicated Wallet.

{% comment %} Once your test users create dedicated or Multi-Counterparty Wallets in the Qredo web app, your Exchange Core Client will receive a WebSocket feed with pending actions of type `ApproveNewWalletConnect`. {% /comment %}

### 5. Exchange: process Wallet connection request

On the Exchange Core Client side, you will receive the feed with a pending Wallet connection request. The format is similar to the other requests pending custody and is specified by its unique `action_id` but this time it is of type: `ApproveNewWalletConnect`

```json
{
  "coreClientID": "6kC7C...Vuz4W",
  "expireTime": 1642766156,
  "id": "<action_id>",
  "status": "pending",
  "timestamp": 1642762556,  
  "type": "ApproveNewWalletConnect"
}
```

You must approve or reject the connect Wallet request before it expires.

{% callout type="note" title="User can resubmit Connect Wallet request" %}
The end user Wallet request for connecting remains in **Pending** status until approved/rejected by the Exchange Core Client. If the request expires, the user can resubmit the request. They have the option to edit the requested metadata. Obviously, this will result in a new request in your exchange feed.
{% /callout %}

#### Get details of a connection request

Use `GET /action/{action_id}` to retrieve various details of the Wallet connection request, including end-user metadata input.

{% callout type="note" title="View action details of any status, not just `pending`" %}
Similarly to the [Core Client approver](/developer-guides/signing-agent/v1/connect-core-client#get-details-of-a-specific-action), you can use this API call to view details of any action, regardless of its status, whether approved, rejected, expired or pending.
{% /callout %}

#### Example request: `GET /action/{action_id}`

```bash
GET /coreclient/{client_id}/action/{action_id}
```

#### Example response: `GET /action/{action_id}`

The response contains various connection-related data including the end-user metadata input.

```json
{
  "id": "<action id>",
  "coreClientID": "<Exchange Core Client id>",
  "type": "ApproveNewWalletConnect",
  "status": "pending",
  "timestamp": "<timestamp of request submission>",
  "expireTime": "<Epoch timestamp when action expires if not approved/rejected>",
  "details": {
    "clientData": {
      "customer_email": "<Qredo user sign-in email>",
      "customer_id": "<Qredo user ID with Exchange>"
    },
    "wallet": {
      "walletID": "<address of connection request Wallet>",
      "name": "Dedicated Eth test - custom policy",
      "asset": "ETH-TESTNET",
      "type": 1,
      "typeCode": "dedicated"
    }
  }
}
```

#### Approve connection request

Use `PUT /action/{action_id}` to approve a pending connection request. A successful response returns a `HTTP 200 OK` confirmation.

#### Example request: `PUT /action/{action_id}`

With this API resource, you pass your Core `client_id` and `action_id` as URL parameters. 

```bash
PUT /coreclient/{client_id}/action/{action_id}
```

#### Example response: `PUT /action/{action_id}`

The approve response returns `HTTP 200 OK`.

```json
{"code":200,"msg":"OK"}
```

#### Reject connection request

Use  `DELETE /action/{action_id}` to reject a pending connection request. A successful response returns a `HTTP 200 OK` confirmation. You can only reject actions in `pending` status.

#### Example request: `DELETE /action/{action_id}`

With this API resource, you pass your Core `client_id` and `action_id` as URL parameters.

```bash
DELETE /coreclient/{client_id}/action/{action_id}
```

#### Example response: `DELETE /action/{action_id}`

The reject response returns `HTTP 200 OK`.

```json
{"code":200,"msg":"OK"}
```

The end user will see a relevant message when viewing the respective Wallet in the Qredo web app: "Counterparty Request Rejected".

### 6. End user: confirm that Wallet is activated

After approval on the exchange side, the Wallet status changes to **Active** in the Qredo Web app. To confirm that, sign in to the Qredo Web app with your test account and open the fund containing the Wallet. After the Wallet has been approved, the user can add assets by deposit or transfer in transactions.

### 7. Test other exchange actions

Use your Exchange Core Client `id` to perform these API actions with your exchange:

* Fetch a list of all Wallets connected to your exchange:

```bash
GET /coreclient/{client_id}/wallets
```

* View details of a connected Wallet, including assets and balance:

```bash
GET /wallet/{wallet_id}
```

* View transaction history of a connected Wallet:

```bash
GET /wallet/{wallet_id}/history
```

* Connect to a [live feed](#dedicated-wallets-feed) of connected *dedicated* Wallet transactions by listening to the following WebSocket feed:

{% comment %} how does that work? I connected successfully but does this feed only return connected Wallets associated with... a single API key or what?) {% /comment %}

```bash
wss://api.qredo.network/api/v1/p/wallets/feed
```

For more info on Core Client transactions, see [approving and rejecting](/developer-guides/signing-agent/v1/connect-core-client) transactions with the Core Client.

{% callout type="note" title="You can now go live" %}
If you have completed all steps from above, contact your Qredo account manager and request to go live. When your exchange is live, all Qredo users can choose it when creating a *dedicated* or *multi-party* Wallet.
{% /callout %}


---


## Exchange custody and sweeps

This section describes the exchange custody over transactions and custody of dedicated and Multi-Counterparty Wallets.

{% callout type="note" title="Exchange standpoint: Dedicated vs Multi-Counterparty Wallets" %}
An Exchange acts as an additional approver for assets transferring out of a *dedicated Wallet*. With the *Multi-Counterparty* Wallet, the exchange doesn't perform custody on transfer out transactions. Therefore, exchange custody over Multi-Counterparty Wallets is like a stripped down version of exchange custody over dedicated Wallets.
{% /callout %}

### Dedicated Wallets feed

Apart from the Exchange Core Client, you can additionally connect to a Core Client live WebSocket feed of connected *dedicated* Wallet transactions. Once a dedicated Wallet is connected, the feed publishes data upon any transfer in AND transfer out transaction.

As an example, you can use the [Signing Client](/developer-guides/partner-api/sign-api-calls#partnerapi-signing-client) to [connect](/developer-guides/signing-agent/v1/connect-core-client) to the WebSocket feed with connected dedicated Wallet transactions.

{% tabs %}

{% tab label="Mac/Linux" %}
1. Launch the CLI and navigate to the directory where the Signing Client is located.
2. In the CLI, type the following: 
```
./partnerapi-sign websocket
```
3. The prompt will ask you to enter the WebSocket URL. Paste in the following link:
```bash
wss://api.qredo.network/api/v1/p/wallets/feed
```
{% /tab %}

{% tab label="Windows" %}
1. Launch `cmd.exe` and navigate to the directory where the Signing Client is located.
2. In the `cmd` interface, type the following: 
```
partnerapi-sign.exe websocket
```
3. The prompt will ask you to enter the WebSocket URL. Paste in the following link:
```bash
wss://api.qredo.network/api/v1/p/wallets/feed
```
{% /tab %}

{% /tabs %}

Upon success, the CLI will print out `connected`.

{% callout type="note" title="`wallets` feed is per API key and not per Core Client `id`" %}
 Notice that the feed is not per Core Client but rather than per API key. This means that if you have more than one Exchange Core Client per account, it will be your responsibility to process and store the data per Exchange Core Client.
{% /callout %}

#### Wallets feed object

The data object of Wallet's feed transactions is similar to that of `GET /wallet/{wallet_id}/history`:

```json
{
  "Status": "approved",
  "amount": -100005000,
  "asset": "ETH-TESTNET",
  "chain_tx_id": "<on-chain tx ID, such as: 9b91468bb5a5e......**724c1d1f927>",
  "fee": -5000,
  "net_amount": -100000000,
  "sweep_tx": false,
  "timestamp": "<Epoch timestamp when tx goes through, e.g. 1644500739>",
  "tx_id": "24vAO8...****8juqs",
  "wallet_id": "GguoQp2yV......9KcA5jtdj4y"
}
```

When `amount` is a negative value, this marks a *transfer out* (outbound) transaction, and vice-versa: a positive `amount` value indicates a *transfer in* (inbound) transaction.

{% callout type="note" title="Details on tx-specific parameters" %}
* `Status`: tx status is always `approved` in this feed.
* `amount`: tx amount equals the sum of `fee` and `net_amount`: `|amount|=|net_amount|+|fee|` with every transfer out tx. With *transfer in* transactions, the `fee` equals `0`, so `|amount|=|net_amount|`.
* `asset`: tx asset symbol type (BTC, ETH, etc.).
* `chain_tx_id`:  the on-chain tx ID. {% comment %} This ledger doesn't appear on  web app {% /comment %}
* `fee`: with outbound transactions, this is the portion of the asset amount, needed for the tx. With inbound transactions, always equals `0`.
* `net_amount`: the amount of asset in the transaction (excluding fee, if any).
* `sweep_tx`: a flag indicating if this is a sweep transaction or not.
* `timestamp`: when on-chain tx ID is written in ledger. {% comment %} CONFIRM {% /comment %}
* `tx_id`: transaction ID. {% comment %} Use that `tx_id` to query details about this tx.{% /comment %}
* `wallet_id`: the ID of connected Wallet with current tx.
{% /callout %}

The feed data object doesn't contain details, such as origin Wallet address with inbound transactions; or destination Wallet ID with outbound transactions.

### Handling of connected Wallets

When connected, the Exchange Core Client receives a notification with action `id` details about each withdrawal or transfer request, submitted by the user.

Use the action `id` supplied in the feed to fetch pending transaction details, including the tx Wallet ID, asset type and amount:

```bash
GET /coreclient/{client_id}/action/{action_id}
```

Learn more about [`GET /action/{action_id}`](/developer-guides/signing-agent/v1/connect-core-client#get-details-of-a-specific-action).

* Approve the transaction if the resulting balance is greater than any outstanding orders:

```bash
PUT /coreclient/{client_id}/action/{action_id}
```

Learn more about [`PUT /action/{action_id}`](/developer-guides/signing-agent/v1/approve-core-client#approve-action-pending-custody).

* Reject the transaction if the resulting balance is less than any outstanding orders:

```bash
DELETE /coreclient/{client_id}/action/{action_id}
```

Learn more about [`DELETE /action/{action_id}`](/developer-guides/signing-agent/v1/approve-core-client#reject-action-pending-custody).

{% callout type="note" title="Transactions out a dedicated Wallet undergo custody twice" %}
The pending transaction must be approved by both exchange and approver. The exchange is not necessarily the same as the approver of the user’s Wallet.
{% /callout %}

### Sweep transactions

On Qredo, a *sweep* is a transaction that allows an exchange to transfer assets out of a connected Wallet without undergoing Wallet's custody. The transaction must still be approved by the Exchange Core Client but the Wallet/fund approvers do not participate in the sweep approval.

The exchange can perform sweep transactions out of both dedicated and Multi-Counterparty Wallets.

Withdraw assets from a dedicated or multiparty Wallet; e.g. to execute a limit order.

* Perform a sweep:

```bash
POST /coreclient/{client_id}/sweep
```

#### Request body

You must specify a destination Wallet.

```json
{
  "wallet_id": "<dedicated wallet ID>",
  "recipient_wallet_id": "<destination Wallet ID>",
  "expires": 1640998800,
  "reference": "CX15R99XX",
  "partner_txID": "",
  "send": {
    "symbol": "BTC",
    "amount": 800
  }
}
```

* The Core Client must approve or reject the sweep request.

{% callout type="note" %}
Sweep into any Qredo Wallet with the API or a browser Wallet.
{% /callout %}

---

## View Wallet


As an exchange, you can query Connected Wallets: a logical step before proceeding with transaction approvals or sweep transactions.

### View transaction history of a connected Wallet

* View transaction history:

#### Example request: `GET /wallet/{wallet_id}/history`

```bash
GET /wallet/{wallet_id}/history
```

#### Example request: `GET /wallet/{wallet_id}/history`

* Connect to a [live feed](#dedicated-wallets-feed) of connected *dedicated* Wallet transactions by listening to the following WebSocket feed:

{% comment %}
how does that work? I connected successfully but does this feed only return connected Wallets associated with... a single API key or what?)
{% /comment %} 

```bash
wss://api.qredo.network/api/v1/p/wallets/feed
```

