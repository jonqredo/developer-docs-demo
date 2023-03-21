---
comment: New granular level, as requested by Riccardo
title: Prerequisites
author: HB
---


To use the Signing Agent, you must have a:

## 1. Have a [Qredo account](https://qredo.network/register) or a [Sandbox account](/user-guides/sandbox) linked to the Qredo Web App

{% callout type="note" title="Enable testnet assets" %}
While not mandatory, Qredo strongly recommends you perform API tests using [testnet assets](/user-guides/testnet). A [Sandbox account](/user-guides/sandbox) is used for integration testing and only uses testnet assets.
{% /callout %}


## 2. Meet all the requirements to use the [Partner API](/developer-guides/partner-api/get-started).

2.1 Set up an account with [Partner API](/developer-guides/partner-api).

2.2 A Signing Agent is part of a custody policy. To create a custody policy, you must have the governing entity in place for the API you will use:

{% tabs %}
{% tab label="Partner API" %}
- You **must** have an existing Company registered within Partner API.  
See [the guide](/developer-guides/partner-api/walkthrough/01-create-company) or the API specification for [POST /company](https://developers.qredo.com/partner-api/api/swagger/#tag/Company/paths/~1company/post).

- You **may** have existing [Fund/s and/or Wallet/s created](/developer-guides/partner-api/walkthrough/03-create-fund).

{% comment %} 
All requests to the Partner API must be signed. Please set aside time to understand [signing API calls for Partner API](/developer-guides/partner-api/sign-api-calls).
{% /comment %}


{% /tab %}
{% tab label="Qredo API" %}
- You **must** have an existing [Organization created from the Web App](https://qredo.zendesk.com/hc/en-us/articles/4404077348753-Creating-an-Organization). 

- You **may** have existing [Fund/s and/or Wallet/s created from within the Web App](https://qredo.zendesk.com/hc/en-us/articles/4404077381265-Creating-a-Fund-Setting-Up-Your-Wallet).
{% /tab %}
{% /tabs %}

## 3. Setup environments

This is a technical integration targeted at developers, it is important to be familiar with:

- Docker
- Open API 3+ integrations
- Generating, using, and managing the lifecycle of [API keys](/user-guides/api-keys) including encoding and timestamping such keys for authentication
- Blockchain transactions including signing and transmitting txs

{% comment %}
Is it necessary to have a Fund or will an Organizational account do?
Is this supported with Personal accounts?
Do we need to discuss Wallets AND Vaults?
{% /comment %}

