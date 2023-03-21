---
comment: Line 41 wip
title: Introduction
author: TK
proofedDate: 20221124
---

## TL;DR

- Qredo Partner API v1 is a RESTful Open API 3 service
- Requests must be [signed and encrypted](/developer-guides/partner-api/generate-keys) using an RSA key
- White-label all the functionality of Qredo Web App and Signing App

{% callout type="warning" title="Deprecation Notice" %}
Qredo Partner API will be deprecated and replaced by Qredo API v2 in Q2 2023.
{% /callout %}

## Overview

This guide introduces the Qredo Partner API. Partner API allows you to perform all operations required to create [Wallets](https://qredo.zendesk.com/hc/en-us/articles/4407297058321-Qredo-Wallet) and move assets under custody across the Qredo network: similar to the user experience via the Qredo Web App. 

{% callout type="note" %}
If you are not familiar with Qredo and want to learn more about our mission and our products, please visit the [Qredo help center](https://qredo.zendesk.com/hc/en-us).
{% /callout %}

The Partner API is a RESTful web service that uses standard HTTPS communication and exchanges JSON-formatted data. The content type of requests (where applicable) and responses is `application/json`.

{% callout type="note" title="Configuration via API is not visible in the Qredo Web App, and vice versa" %}
Qredo provides isolated configuration between Partner API and the Web App. This results in separated management views: the Web App configuration cannot be viewed through the API, and API configuration is not visible via the Web application.

If you are looking for an API that integrates with the Web App, checkout [Qredo API](/developer-guides/qredo-api)
{% /callout %}


## Integrations

Partner API integrates with the [Signing Agent](/developer-guides/signing-agent), a programatic approval service that provides all the functionality that is available to an approver of outgoing Wallet transactions on the [Qredo Signing app](https://qredo.zendesk.com/hc/en-us/articles/4407895569937-Qredo-Signing-App) on mobile devices.


## Who uses Partner API?

The Partner API enables ISVs, Exchanges, Custodians, and Market Makers to operate the Qredo Network programmatically from their own applications. 

{% tabs %}

{% tab label="Institutions" %}
Institutions benefit from decentralized custody over digital assets and a customizable custody suite offering granular control over Wallets and their transactions. The Partner API allows institutions to fully rely on Qredo for custody over their digital assets. Once an Organization (your institution's entity within Qredo) deposits assets in their Qredo Wallets, any transaction out of these Wallets undergoes one or more approvals. The API provides custody policies over transaction approvals and Wallets in a modular way that can fit any business requirement, including automating approval according to pre-defined conditions with Signing Agent.
{% /tab %}

{% tab label="Exchanges" %}
Exchanges may white-label the Qredo product suite to offer Qredo's custody to their own client base. In conjunction with the Signing Agent, the Partner API allows Qredo users to connect one or more of their Qredo Wallets to the exchange (in the Qredo Wallet App). Once connected, the exchange gains control over transactions out of these Wallets, in parallel of the "regular" trusted network of approvers, appointed by the Wallet owner.

Additionally, the exchange can perform sweep transactions that bypasses Wallet approvers to move assets out of these Wallets, i.e. transfer out of a Wallet.
{% /tab %}

{% /tabs %}


## Partner API Overview

The Partner API allows you to perform actions available to the **_Qredo Wallet user_** on the Qredo Network, such as:

* create and manage a *company*
* create and manage a *trusted network*
* create and manage one or more *funds*
* add or remove one or more *Wallets* in fund
* perform transactions (*transfers* and *withdrawals*)
* perform *atomic swaps*

{% callout type="warning" title="API-generated actions not integrated via the Qredo Web App!" %}
Actions performed in the Partner API are not visible in the Qredo Wallet Web App, and vice-versa. This applies to Funds, Wallets, assets and transaction history. This is because the Partner API is designed for institutions and exchanges, not for API control over a regular user account.
{% /callout %}

{% comment %}

### Glossary of terms

* **Asset** Asset is a Level 1 blockchain asset (such as BTC or ETH) that is supported on the Level 2 Qredo blockchain (also called Qredochain).
* **Counterparty** The persons or entities on opposite sides of a transaction. These are also referred to as the sending (initiating) party and receiving (beneficiary) party.
* **Custody** to the governance of outgoing transactions. With Qredo, all transactions out of any Qredo Wallets require an approval, regardless of asset type, .


### Topics in this guide

Currently, this guide covers the following topics:

* Overview (current section): contains introduction and prerequisite subsections to get started with the Partner API:
    * [API introduction](api-intro) discusses basics of the Partner API.
    
    * [Sign up](quickstart) using the Qredo Web App and Qredo mobile App and obtain Partner API access.
    
    * [Generate API keys](generate-keys) and prepare your account for the Partner API.
    
    * [Sign API calls](/developer-guides/partner-api/sign-api-calls) and start with the Partner API.
    
* [Partner API quick start](/quickstart/developer-quick-start): start using the API by creating a *company*, add a *fund* with *trusted network* and *custody policies*, and then proceed with transferring assets.
* [Core client](/developer-guides/signing-agent/v1/set-up-core-client): create, set up and connect a client listener to a WebSocket feed with transactions pending custody.
* [Exchanges quick start](/exchange/exchange-quick-start): set up the Signing Agent and a network of Wallets per user for exchange-monitored and approved transactions.
* [Atomic swaps](/developer-guides/partner-api/atomic-swaps): create atomic swap quote, publish to liquidity hub and retrieve pending quotes.
* [API reference](/api-reference/api-overview): a comprehensive reference with API call details, including JSON payloads and HTTP(S) error codes.
  
{% callout type="note" title="You must enroll for API access" %}
Access to the Qredo APIs is subject to verification.
{% /callout %}


{% /comment %}



## API environments and Base URLs

Production and Sandbox environments are available. However, keys for authentication are different.

### Production

The Production environment allows you to perform both live and Sandbox API calls. You must create and use a dedicated set of keys for each. For test API calls on Production, you will use *Sandbox* keys. For live operations on Production, you will use production keys. See [Generate keys](/developer-guides/partner-api/generate-keys) for more info.

{% tabs %}

{% tab label="BASE URL on Production" %}
    ```curl
    https://api.qredo.network/api/v1/p
    ```
{% /tab %}

{% tab %}{% /tab %}

{% /tabs %}

### Sandbox

The experience on Sandbox is very similar to that on Production. You essentially follow the same process, but with a simplified flow:

- You can only use Testnet assets on Sandbox
- Your API access is enabled by default (no need to apply to be a Partner) 

#### Test environment

{% tabs %}

{% tab label="BASE URL on Sandbox" %}
    ```curl
    https://sandbox-api.qredo.network/qapi/v1/p
    ```
{% /tab %}

{% tab label="Deprecated: Play"  %}
    ```curl
    https://play-api.qredo.network/api/v1/p
    ```
{% /tab %}

{% /tabs %}

## API timestamps

All API timestamps are in Unix Epoch format.

## API requests

The [Partner API](/api-reference/partner-api) is served over HTTPS TLS v1.2+ to ensure data privacy; HTTP and HTTPS with TLS versions below 1.2 are not supported. All API requests must be made over HTTPS. Calls made over plain HTTP will fail. API requests without authentication will also fail. All requests must include a Content-Type of application/json and the body must be valid JSON.

The API requests use standard HTTP methods: GET, PUT, POST, DELETE. Every request submitted to the API must be signed and encrypted using an RSA key. This provides request integrity and adds a security layer in terms of authorization with the API service.

### Request headers

All API resources use the same header format. The Qredo API uses RSA keys to sign and encrypt API requests. Each API call signature is combined with a timestamp or nonce. This ensures that each request is unique and also the response is readable only by the API user who has the private key to decrypt it.

With __every__ API request header, you must supply:

* `accept: application/json`: all payloads are in JSON.
* `x-nonce` or Unix Epoch `x-timestamp`: use either (not both).
* `x-sign`: a unique [signature](/developer-guides/partner-api/sign-api-calls) generated for each API request.
* `X-API-KEY`: the API key [copied from the Qredo Web Application](/developer-guides/partner-api/generate-keys#generate-api-keys-in-qredo-web-application).

{% callout type="note" title="Signatures in API calls headers" %}
The `x-sign` header must contain the signature generated for the API request (including the JSON payload). It is coupled with either the `x-timestamp` header or `x-nonce` generated with the signature.
{% /callout %}

An example request header would contain information as shown:

```bash
-H "accept: application/json" \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo Web App>" \
```

Note that this tutorial doesn't include complete examples in CLI using `curl`. You can copy `curl` API requests from [the specification](/api-reference/partner-api).

{% callout type="note" title="Headers are excluded from examples" %}
In this guide, the request headers are omitted in examples. You will need to generate the signature and timestamp with each API call. To facilitate your understanding and use of signatures, Qredo provides a [dedicated example tool](/developer-guides/partner-api/sign-api-calls#partnerapi-signing-client) to sign and timestamp your requests.
{% /callout %}

### Request examples

In this guide, all requests are presented as `curl` requests:

```bash
curl -X GET https://api.qredo.network/api/v1/p/assets
```

The body of each request (where relevant) is presented as uncompressed JSON.

```json
{"name": "random_inc"}
```

## API responses

Responses use standard HTTP codes to indicate status and errors. Payloads are returned in JSON format.

### Response examples

In this guide, any payload is presented as formatted JSON.

```json
{
  "company_id": "1va2cJ2pqdLSFAN5t1knBrnuMvw",
  "ref":""
}
```

To avoid confusion or accidentally listing real user data, many of the actual values in response examples contain descriptions of expected data enclosed in <>.

```json
{
  "company_id": "<company_hash_id>",
  "total_count": "<total number of trusted parties>",
  "list": [
     {
        "list": {
          "trusted_entity_id": "<user's trusted network ID>",
          "name": "<first and last name>",        
          "address": "<email address>",
          "type": "<user type>", 
          "created": "<Epoch timestamp>"
      }
    }
  ]
}
```
#### Error Messages

* ERROR STRUCTURE

```json
{
  "code": "<ERROR_CODE>",
  "msg": "<ERROR_MESSAGE>",
  "detail": {
    "reason": "<NOT_ALWAYS_PRESENT>"
  }
}
```

* EXAMPLE ERROR MESSAGE

```json
{
  "code": 401,
  "msg": "Unauthorized",
  "detail": {
    "reason": "empty token"
  }
}
```

Qredo uses conventional HTTP response codes to indicate the success or failure of an API request. In general: Codes in the `2xx` range indicate success. Codes in the `4xx`range indicate an error that failed given the information provided (e.g. a required parameter was omitted). Codes in the `5xx` range indicate an error with Qredo servers (rare).

Some `4xx` errors include an error code explaining the error reported.

All errors are returned with the same structure:

Error Code | Meaning
---------- | -------
400 | Bad Request -- The request was not accepted typically due to invalid syntax. Make sure your request is formatted correctly.
401 | Unauthorized -- The request doesn't have permission to perform the request. Make sure you've entered a correct API Key for authentication.
403 | Forbidden -- The API key doesn't have permission to perform the request. Is your account set up? Contact **Support**
404 | Not Found -- The requested resource doesn't exist. Make sure you've formatted the URL correctly.
429 | Too Many Requests -- Too many requests hit the API too quickly. Please try again later. 
50x | Internal Server Error -- Something went wrong on Qredo's end (rare). Please try again later.


## Specific data types

The API uses standard tickers for the supported Layer 1 currencies (also referred to as *assets*). See the full list of [supported assets](https://qredo.zendesk.com/hc/en-us/articles/4407177502097-Supported-Assets) and their tickers.


{% callout type="note" %}
Use the `GET/assets` endpoint to retrieve the up-to-date list of all supported L1 assets and scale of the denomination units of each.
{% /callout %}

### Assets and asset types

The cryptocurrency data type used across API endpoints could be `asset` or `asset types`, and also `code` or `unicode`. For example: `"code":"BTC"`.

### Assets amounts and scale

The asset amounts are presented in base units per currency. For example, the base currency of BTC is called *satoshi* and presents BTC amounts at scale 1:100,000,000. This means that 1 Bitcoin is presented as 100 million satoshi. Likewise, ETH amounts are presented in *gwei* at scale 1:1000 million. This means that 1 ETH equals 1 billion gwei.

## Versioning

A publicly released version of the Partner API will never change in any way that could impact clients' integrations.

Whenever Qredo makes any API change, a new version of an API will be released that clients can choose to upgrade their integration to.


