---
comment: wip more wip items inline
title: Get started
author: TK> HB
proofedDate: 20221122
---

## Prerequisites

To use the Qredo API v1, you must have:

{% comment %} FIXME: Introduce glossary entry for Fund {% /comment %}
- A [Qredo Account](https://qredo.network/register). In case there is an organisation account, you should have access to the administration role.
- A Fund with a [Wallet](/glossary/wallets)


## Step 1: Generate an API Key and secret pair

{% comment %} FIXME: Purple Rain update {% /comment %}
{% comment %} FIXME: Update link to user guide {% /comment %}

To generate an API key and secret pair, you will need to use the [Web UI](https://qredo.network/signin).
To do so, navigate to Account > Settings > API. From this tab you can create an API key and assign the relevant permisison.
Check out [this guide](#) for further details.

**Note:**  It is important to have at least one Fund managing a [Wallet](/glossary/wallets). Calls made using API keys generated for an Organization with no Funds or Wallets will return `null` data.

To read more about [API keys](/glossary/api-keys) including their granular permissions model, check out [Managing API keys and Secrets](api-keys).

## Step 2: Generate your request

Qredo API is a RESTful service. Generate your request using either the [Web3 Wallet API](/developer-guides/api-services/web3-wallet)
or the [Read-only API](/developer-guides/api-services/read-only).


## Step 2: Generate a valid signature for your requests

The signature is a way for the API server to ensure API calls are authorized.
This signature must be attached to the request using the `qredo-api-sig` header.

To generate it:

1. Construct the request to sign in the format: ```[timestamp][method][URL][body]```.

    The timestamp used for signing must be the same one that is used in the header `qredo-api-ts`.
    
    For example, suppose I want to sign an API call to `GET /balance`, with an empty body. The parameters are as following:
    * timestamp: `1647356399`. This timestamp must be the same that is passed as `qredo-api-ts` header
    * HTTP method: `GET`
    * URL: `https://api.qredo.network/qapi/v1/balance`
    * body: _none_

    The resulting string that will need to be signed is: `1647356399GEThttps://api.qredo.network/qapi/v1/balance`

2. Using the decoded secret, sign the request as constructed in Step 1 with the HMAC-SHA256 algorithm. Note that if you are storing the secret as it is displayed in the web app, **you will need to base64-decode it first**.
3. Encode the signed payload with URL-safe Base64 encoding.
4. Assign the generated signature to the `qredo-api-sig` header.

Check out [the relevant page](sign-api-calls) for further examples and code snippets you can re-use.

## Step 3: Test your integration

As you develop your integration, please use the sandbox environment, using as base URL for the endpoint:
```
https://sandbox-api.qredo.network/qapi/v1
```
You can also use [the API testing tool](test) to allow you to send valid requests directly from the CLI or from a basic web interface.

## Step 4: Deploy your integration
To go live with your integration, simply switch your base endpoint to:
```
https://api.qredo.network/qapi/v1/
```

Congrats! 🎉

You are now using Qredo API v1.


