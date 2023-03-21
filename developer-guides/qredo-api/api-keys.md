---
comment: WIP 1. Why can't i link to heading within page? "#'administrators-and-the-qredo-api-v1 2. needs link to roadmap. 3. WIP once you can create Organizations from API, this needs an update. 4. TK>Question - Can the Trader initiate a transferOut of any wallet within the Org? JH -- fixed size images will mess up any responsive design, solution?
title: Manage API keys and secrets
author: TK > HB
proofedDate: 20221122
---

To use the API for Personal and Organization accounts, you must generate an *API key* and *API secret* pair in the Qredo Web App. 

## Prerequisites

To generate Qredo API keys, you must have a [Qredo account](https://qredo.network/register), and for an Organization account, you must have the [administrator role](/glossary/accounts).

## Assign permissions to an API key: basic flow

Follow the [UI guide](/user-guides/api-keys#assign-permissions-to-an-api-key) to assign permissions to keys from the Web App.

The following permissions apply:

Permission  | Description
---|---
 Balance | allows holders of the key to access the `/balance` endpoint of the [Read-only API](/developer-guides/api-services/read-only). 
 Web3 Transactions | allows holders of the key to submit [Web3 API Wallet transactions over API](/developer-guides/api-services/web3-wallet) and view transaction status.
 Read-only | allows holders of the key to access all [Read-only API](/developer-guides/api-services/read-only) endpoints, including balance, account info, Fund info, Fund ledger, transaction info, etc.

You are ready to [sign API calls](sign-api-calls).

