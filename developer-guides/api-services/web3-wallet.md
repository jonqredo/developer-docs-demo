---
comment: error? approvers may be active at any level -- Organizational, Fund, or Wallet. Is this true for Fund?
author: HB
title: Web3 API Wallet services
proofedDate: 20021209
---

| Supported in | Prerequisites |
| ------------ | ------------- |
| v1 | [Requirements](#prerequisites) |

## TL;DR

- Supports programmatic generation of Web3 interactions
- Provides custody over assets
- Initiates approval flow through to transaction broadcast-ready
- Integrates with Signing Agent

## Prerequisites

Further to the [basic requirements](/developer-guides/qredo-api#prerequisites), you need the following to use the Web3 API Wallet service: 

- An Organization with the Connected Wallet feature enabled. To enable a Connected Wallet, contact support@qredo.com.

- A Fund with an associated [Web3 API Wallet *and* credits within your Qredo Fee Station](#create-a-defi-api-wallet). It's recommended that the Web3 API Wallet custody is set up with multiple approvers. Such [approvers](/glossary/approvals) may be active at the level of Organization or Wallet. They may be trusted parties or automated Signing Agents. 

## Service overview

The Web3 API Wallet is a [Connected Wallet](/glossary/wallets) service that allows Qredo customers to create transactions from Layer 1 Wallets on any supported network while benefiting from Qredo's secure custody suite.

{% callout type="note" title="Transaction support" %}
Qredo API v1 supports [Type 0, 1](/glossary/transactions#legacy-transactions), and [2 (EIP-1559)](/glossary/transactions#type-2) transactions. Learn more in the [Knowledge Base](/glossary/transactions).
{% /callout %}

## Integrations

### Whitelisting

The Web3 API Wallet supports the allowlisting of addresses to control where assets may be sent. 

* Learn more in the [help center](https://qredo.zendesk.com/hc/en-us/articles/10626460462097-Whitelisting-Addresses-Beta-). 

### Read-only

Fulfill your compliance and audit reporting requirements using the [Read-only reporting service](read-only).

### Signing Agent

The Web3 transaction service works together with the [Signing Agent](/developer-guides/signing-agent) to automate the approval flow according to your customized ruleset.


## Web3 API services

The Web3 API Wallet service provides endpoints that support creating transactions and viewing a transaction's status. Transactions submitted via this Web3 API Wallet are available from the Qredo Web App interface.


Endpoint | Description | Permission
-------- | ----------- | ----------
[`POST /transaction`](/api-reference/qredo-api#tag/Web3-API/paths/~1transaction/post) | Submit a transaction for signing and approval | Write access
[`GET /transactions/{tx_id}/approvals`](/api-reference/qredo-api#tag/Web3-API/paths/~1transactions~1%7Btx_id%7D~1approvals/get) | Get approvals' status for a specific transaction | Read only
[`GET /transactions/{tx_id}/status`](/api-reference/qredo-api#tag/Web3-API/paths/~1transactions~1%7Btx_id%7D~1status/get) | Get status of a specific transaction | Read only 
[`Get /transactions/feed`](/api-reference/qredo-api#tag/Web3-API/paths/~1transactions~1feed/get) | WebSocket feed of status of transactions.| Read only  

See the [Qredo API v1 specification](/api-reference/qredo-api) for detailed information on how to use these endpoints.

### Service use cases

#### 1. Sign transactions for asset transfers

[`POST /transaction`](/api-reference/qredo-api#tag/Web3-API/paths/~1transaction/post) allows trusted parties to create a new transaction, send it for approval, and receive it back, broadcast-ready.

* Learn more about [transactions](/glossary/transactions)

#### 2. Sign smart contract interactions

`POST/transaction` allows trusted parties to create a new instruction to a smart contract, send it for approval, and receive it back broadcast-ready.

* Set `value` to "0" if the request doesn't transfer assets.

#### 3. View approval status of a transaction

Two endpoints provide approval status:

- `Get /transactions/feed` provides a summary of the approval status for a transaction

- `GET /transactions/{tx_id}/approvals` provides the details of a specific transaction based on the transaction ID.


## Create a Web3 API Wallet

Once you have Connected Wallet [permission enabled](#prerequisites), you may add the Web3 API Wallet from within an **Organization** account via the Qredo Web App.

{% callout type="note" %}
Personal accounts don't support Web3 API Wallets.
{% /callout %}

1. Follow the [instructions to add a Connected Wallet](https://qredo.zendesk.com/hc/en-us/articles/6502894088593-Adding-a-Wallet).

1. Follow the [instructions to select a **Web3 API** Wallet](https://qredo.zendesk.com/hc/en-us/articles/11561617261585-Creating-a-WalletConnect-Wallet).

    {% callout type="warning" title="Select an approval policy" %}
    Setting an [approval policy](/glossary/approvals) is a key step in creating secure custody over your assets.
    {% /callout %}

1. Top up your Qredo Station.

    {% callout type="note" title="Learn more about managing fees with Qredo Station" %}
    More information about fees is available in the [help center](https://qredo.zendesk.com/hc/en-us/articles/10869892601617-Qredo-Fees).
    {% /callout %}


{% callout type="note" title="Programmatic control" %}
    Remember to [create API keys and customize their access](/developer-guides/qredo-api/generate-keys) over your Web3 API Wallet. 
{% /callout %}