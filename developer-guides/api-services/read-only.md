---
comment: UX issue x1 user did not know how to fufill prerequisites (did not click link)
author: HB
title: Read-only services
proofedDate: noProof
---

Supported in | Prerequisites
------------ | -------------
v1 | [requirements](#prerequisites)

## TL;DR

- Reporting service
- Supports Standard and Connected Wallets

## Prerequisites

Further to the [basic requirements](/developer-guides/qredo-api#prerequisites), you need:

- [API keys set with permission](/user-guides/api-keys#assign-permissions-to-an-api-key) to use Read-only services


## Service overview

The Read-only service provides a reporting service to Qredo customers and their trusted parties. Read-only provides basic information about your:

- Account
- Organization
- Fund
- Wallet


## Integrations

Read-only's reporting service complements the Standard Wallet and the Connected Wallet, [Web3 API Wallet's](web3-wallet) reporting needs.


## Read-only services

The Read-only API provides a suite of endpoints that return data related to Personal and Organizational accounts; Wallets; and Funds.


Endpoint | Description | Permission
-------- | ----------- | ----------
[`GET /account`](/api-reference/qredo-api#tag/Read-only-API/paths/~1account/get) | Get account info. Responses differ for Personal or Organization accounts | Read only
[`GET /balance`](/api-reference/qredo-api#tag/Read-only-API/paths/~1balance/get) | Get account balance | Read only
[`GET /funds/` ](/api-reference/qredo-api#tag/Read-only-API/paths/~1funds/get)| Get a list of all account Funds | Read only
[`GET /funds/{fund_id}`](/api-reference/qredo-api#tag/Read-only-API/paths/~1funds~1%7Bfund_id%7D/get) | Get info for a specific Fund | Read only
[`GET /funds/{fund_id}/ledger`](/api-reference/qredo-api#tag/Read-only-API/paths/~1funds~1%7Bfund_id%7D~1ledger/get) | Get transaction history of a specific Fund  | Read only
[`GET /trustedparties`](/api-reference/qredo-api#tag/Read-only-API/paths/~1trustedparties/get) | Get a list of trusted parties. Works with personal accounts only | Read only
[`GET /wallets/{wallet_id}/history` ](/api-reference/qredo-api#tag/Read-only-API/paths/~1wallet~1%7Bwallet_id%7D~1history/get)| Get transaction history of a specific Wallet | Read only

See the [Qredo API v1 specification](/api-reference/qredo-api) for detailed information on how to use these endpoints.

{% callout type="note" title="Programmatic control" %}
Remember to [create API keys and customize their access](/developer-guides/qredo-api/generate-keys). 
{% /callout %}