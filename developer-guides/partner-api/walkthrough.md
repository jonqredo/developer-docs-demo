---
comment: Consider adding `POST /wallet`.
title: Walkthrough
author: TK
---

The step-by-step walkthrough is designed to help you get started with the Qredo Partner API. The steps are organized in a basic flow to have your account set up for all basic asset transfer scenarios. This section doesn't discuss the Signing Agent.

## Overview of steps

This section presents a sequence of suggested steps to help you get started with the Partner API.

* **Step 1.** [Create a company](/developer-guides/partner-api/walkthrough#step-1-create-a-company) and get your *company ID*. You'll be completing all remaining steps on behalf of your company.

* **Step 2.** [Add a trusted party](/developer-guides/partner-api/walkthrough#step-2-add-a-trusted-party) to start building your trusted network of approvers. Your newly-enlisted approver must accept an enrollment request before they can be eligible for performing custodial actions.

* **Step 3.** [Create a Fund](/developer-guides/partner-api/walkthrough#step-3-create-a-fund) and appoint approvers as created in the previous step. Your company requires a Fund that holds Wallets you can deposit assets to. In this step you create Fund custodial policies: one for *transactions* and one for *withdrawals*.  At this step, you also appoint trusted parties with the respective policy.

* **Step 4.** [Perform a deposit](/developer-guides/partner-api/walkthrough#step-4-perform-a-deposit) transfer assets from a Level 1 Wallet to your Fund Wallet address.

* **Step 5.** [Perform a transfer](/developer-guides/partner-api/walkthrough#step-5-perform-a-transfer) transaction within the Qredo Network: move assets from one Qredo Wallet to another. Completion of this step requires a custodial action.

* **Step 6.** [Perform a withdrawal](/developer-guides/partner-api/walkthrough#step-6-perform-a-withdrawal) and finalize the walkthough by transferring assets to a whitelisted Level 1 Wallet: one that resides outside the Qredochain, such as BTC, ETH, etc. Completion of this step requires a custodial action.

Each of these steps is detailed in a dedicated subsection.

{% callout type="warning" title="Whatever you do in the API is not visible in the Qredo Web app" %}
You will use the same account to access the Qredo Web app and the API. However, what happens in the Web app will not be visible or accessible in the API, and vice versa. For example, Funds, Wallets and assets contained in these Wallets will remain where created: either in Web App or the API.
{% /callout %}

## High-level overview of API-specific terms

Before you continue, you should understand the underlying concepts for operations on the Qredo Network:

**All transactions across and out the Qredochain undergo custody. In other words: all transactions out a Qredo Wallet require approvals (or rejections) by approvers that you appoint. These include transfers and withdrawals.**

You will be dealing with one or more of the following terms in most most API use cases:

{% callout type="note" title="A short glossary with Qredo-specific terms" %}
**Company** - This is the entity on behalf of which you perform most API call actions. Think of it as an office that manages one or more *Funds* and employs all *approvers* for asset transfers.

**Trusted party** - This is the Qredo approver: a dedicated Qredo account who *approves* or *rejects* transactions. The collection of trusted parties in a company is called a *trusted network*. Every trusted party user performs custody via their Qredo mobile Signing app. The API offers a dedicated user type for "API custody": the [Signing Agent](/developer-guides/signing-agent). You will learn about it in the next chapter.

**Fund** - The Fund is the actual sub-entity to a company that contains Qredo *Wallets* and the approval rules for transactions with these Wallets: the so-called *custodial policies*. These policies also include the trusted parties that perform custody over transactions within that Fund. 

**Custodial policies** - A custodial policy defines the number of approvals (also called the *threshold*) necessary for a *transfer* or *withdrawal* transaction to be completed; plus it also includes the approvers eligible to perform these approvals. Every Fund has a "global" policy for transfers and withdrawals. A Wallet can have custom policies as well but this is not required. If a given Wallet lacks policies, the global ones apply. Note that each policy definition includes a selection (a subset or all) of the company trusted parties for custodial actions.
{% /callout %}

Do not worry if these concepts are not entirely clear yet. You will learn more about each when you complete all steps.

## API specifics in this walkthrough

All steps are designed to follow a natural flow to build up comfort with Qredo specifics.

* API resources in titles and explanation paragraphs are shown simplified with base URLs excluded: e.g. `POST /company`.
* Not all API calls are covered: just the ones to get you started, so visit the [Swagger UI documentation](https://play-swagger.qredo.network/) on Sandbox for more info.
* Headers are not included in request examples. See [Sign Partner API calls](/developer-guides/partner-api/sign-api-calls#authorize) for more info.
* Descriptions of body parameters are added in their respective request and response examples in the stead of actual values.
* Some steps require the use of a trusted party. For testing, custodial actions can be performed by either a Qredo account that you own or a [Signing Agent](/developer-guides/signing-agent) set for custody. However, this walkthrough will not go into detail with Core Client use.

{% callout type="note" title="Use testnet assets when testing" %}
Qredo strongly recommends that you use testnet assets for test transactions until you get familiar with the Partner API.
{% /callout %}

## Step 1: Create a Company

This section guides you through creating a Company. Nearly all API calls are performed on behalf of a Company, including transactions. You must have at least one Company but you can have several for separate asset flows.

The Company sits on top of the hierarchy. Each Company has a list of custodians (approvers) that approve transactions out of Wallets that belong to a Company. 

To create a Company, use the [`POST /company`](https://developers.qredo.com/partner-api/api/swagger/#tag/Company/paths/~1company/post) API endpoint. You must supply a `name`. A successful response returns the `company_id`. This `company_id` is passed in all following API calls.

{% callout type="warning" title="Your Companies are not visible in Web App" %}

The Company is similar to an Organization in the Qredo Wallet Web App. However, just like Organizations are not visible in the API, the companies are not visible in the Web App.

{% /callout %}

### Example request: `POST /company`

This example creates a Company called `random_inc` to this endpoint:

```bash
curl -X POST "https://api.qredo.network/api/v1/p/company" 
```

#### Request body

`name` is required. All other parameters are optional. You can update any of these later, including the name.

```json
{
  "name": "random_inc",
  "city": "<name of city, e.g. 'Paris'>",
  "country": "<ISO 3166-1 country code, e.g. 'FR'>",
  "domain": "randomincorporated.com",
  "ref": "<a Company reference ID, e.g. '9827feec-4eae-4e80-bda3-daa7c3b97add'>"
}
```

### Example response: `POST /company`

Upon success, the response returns the `company_id` hash as a string value.

```json
{
  "company_id": "1va2cJ2pqdLSFAN5t1knBrnuMvw",
  "city": "Paris",
  "country": "FR",
  "domain": "randomincorporated.com",
  "ref": "9827feec-4eae-4e80-bda3-daa7c3b97add"
}
```

## Step 2: Add a trusted party

This section guides you through adding approvers to your Company. [Approvers](/glossary/approvals) may be (human) trusted parties i.e. Signing App approvers or automated [Signing Agents](/developer-guides/signing-agent). 

{% callout type="note" title="Trusted parties approve *transfer out* transactions" %}

Trusted parties perform custody on all transactions that [move assets out of a Qredo Wallet](/glossary/transfers).

{% /callout %}

{% callout type="warning" title="Your trusted parties are not visible in the Qredo Web App" %}

As with Companies instantiated via the API, the trusted parties you enlist with your Company will not be visible in the Web App.

{% /callout %}

### 1. Add Signing App approver/s

{% callout type="note" title="Policies are editable" %}

You can always add or remove trusted parties at a later stage.

{% /callout %}

#### 1.1. Enlist a trusted party

To enlist a trusted party, use [`POST /trustedparty`](https://developers.qredo.com/partner-api/api/swagger/#tag/TrustedNetwork/paths/~1company~1%7Bcompany_id%7D~1trustedparty/post):

* Identify the Company by `company_id` in the path. 
* Pass the trusted party user's registered email `address` in the body of the request.  
This must be the email address used with their Qredo account.

##### Example request:

```curl
curl -X 'POST' \
  'https://play-api.qredo.network/api/v1/p/company/{company_id}/trustedparty' \
  -H 'accept: application/json' \
  -H 'x-timestamp: 1668069089' \
  -H 'x-sign: r76j7xH....QpUxEw' \
  -H 'X-API-KEY: eyJrZXl...6dHJ1ZX0' \
  -H 'Content-Type: application/json' \
  -d '{"address":"approver@qredouser.com"}'
```

##### Example response:

```
{"code":200,"msg":"OK"}
```

{% callout type="note" title="You can add several trusted parties at once" %}

You can supply several entries in the JSON body, for example:

```json
{ 
"address": "approver@qredouser.com",
"address": "anotherapprover@qredouser.com",
"address": "yetanotherapprover@qredouser.com"
}
```

{% /callout %}

A successful response returns a `HTTP 200 OK` confirmation via the API, and the appointed user will receive a notification on their mobile device with the Qredo Signing App installed.

{% callout type="warning" title="Enlisted trusted parties cannot perform custody yet" %}

This step add users to your trusted network. They will not be able to approve transactions until appointed to do so, as described in the next guide: [Step 3. Create a fund](/developer-guides/partner-api/walkthrough#step-3-create-a-fund).

{% /callout %}

#### 1.2. Wait for trusted party approval

Your appointed user must approve (authorize) their addition to your trusted network via the Mobile Signing App.

#### 1.3. Verify trusted party addition

Either communicate with your nominated trusted parties to verify their acceptance, or check [`GET /trustedparty`](/developer-guides/partner-api/walkthrough/02-add-trusted-party#how-to-verify-trusted-party-addition) to retrieve a list of all trusted parties in a Company. 

### 2. Add a Signing Agent approver

A Signing Agent may be added as a *trusted party* to your Company.

#### 2.1. Enlist a Signing Agent as an approver

To enlist a trusted party, use [`POST /trustedparty`](https://developers.qredo.com/partner-api/api/swagger/#tag/TrustedNetwork/paths/~1company~1%7Bcompany_id%7D~1trustedparty/post):

Identify the Company by `company_id` in the path. Pass the Signing Agent's identifier, `agentID` as the `address` in the body of the request.


{% callout type="note" title="You can add several Signing Agents at once" %}

You can supply several entries in the JSON body, for example:

```json
{ 
 "address": "HNRosks34HS0ILtlRtEzNlITDlPEoirgXTxzY52qPG7w",
 "address": "CBApocs97bS7SgE21Ht5xRHFWRbcrH9AMZHgsoUd2gpp",
 "address": "JSOwasg92vW1eJNRGz3qb20K7GUxy7f5jtEBZrX4bTsa"
}
```

{% /callout %}

{% comment %} ##### 2 What is the acceptance behavior for SA? todo Abel missing piece? {% /comment %}

#### 2.2. Verify trusted party addition

Verify the addition of your Signing Agent with [`GET /trustedparty`](/developer-guides/partner-api/walkthrough/02-add-trusted-party#how-to-verify-trusted-party-addition) to retrieve a list of all trusted parties in a Company. 

### How to verify trusted party addition

##### Example request: `GET /trustedparty`

Retrieve a list of all trusted parties in a Company. Again, you need to pass the `company_id` as a URL parameter.

```bash
curl -X GET "https://api.qredo.network/api/v1/p/company/{company_id}/trustedparty" 
```

No body parameters are sent with this request.

##### Example response: `GET /trustedparty`

A successful response returns a list with trusted network members.

```json
{
  "company_id": "<company ID>",
  "total_count": "<total number of trusted parties>",
  "list": [
     {       
        "list": {
          "trusted_entity_id": "<user's approver ID>",
          "name": "<first and last name>",        
          "address": "<email address>",
          "type": "<user type>", 
          "created": "<Epoch timestamp when user was added as a trusted entity>" 
      }
    }
  ]
}
```

Obviously, your selected trusted party must accept the enrollment request before you can expect this response to include them. 

{% callout type="note" title="Add approvers to a Fund" %}

Even after becoming part of your trusted network, trusted parties cannot perform custodial actions. Before you can add them to a custody policy, you must [create a Fund](/developer-guides/partner-api/walkthrough#step-3-create-fund) with Wallet/s.

{% /callout %}

## Step 3. Create a Fund

{% comment %}
Most of this is evergreen material that should be moved to knowledge base wip Gabe example // Consider adding `POST /Wallet`.
{% /comment %}

A *Fund* is the sub-entity to the Company that contains one or more *Wallets* and defines the *custodial policies* for transfers and withdrawals of assets out of these Wallets. With Qredo, a Wallet always belongs to a Fund and any transaction that moves assets out of a given Qredo Wallet, undergoes custody.

{% callout type="note" title="Every transaction out of a Qredo Wallet requires an approval" %}

*  On the Qredo Network, a transaction to move assets out of a Qredo Wallet can only take place after it has been approved by the approvers of that Wallet. This includes transactions out of a Wallet: transfers or withdrawals.
*  The approval process on Qredo requires that a minimum threshold of enlisted approvers must provide their approval for a transfer out transaction to go through.

{% /callout %}


### Fund and Wallet definition

A Company must have at least one Fund. The Fund can contain one or multiple Wallets In terms of the Partner API, a `Fund` definition contains:

  * a Fund custodial policy: `custodygroup_withdraw`. The policy includes its dedicated list of appointed approvers and a `threshold` value: the number of required approvals. Default configuration to all Wallets with no such custom policy configured.
  * a Fund transfer out custodial policy: `custodygroup_tx`. The policy includes its dedicated list of appointed approvers and a `threshold` value: the number of required approvals. Default configuration to all Wallets with no such custom policy configured.
  * one or more `wallets` (per asset, such as BTC or ETH). You can configure custodial policies in the Wallet definition to override Fund policies. 

#### Fund definition object

Use `POST /company/{company_id}/fund` to create a Fund. You can see the simplified view of what the Fund definition contains.

```json
{
  "name": "Fund 01",
  "description": "Fund for demonstration purposes",
  "custodygroup_withdraw": {
    "threshold": 2,
    "members": [ list of withdrawal approvers
    ]
  },
  "custodygroup_tx": {
    "threshold": 2,
    "members": [ list of tx approvers
    ]
  },
  "wallets": [ list of Wallet definitions
  ]
    }
  ]
}
```

Make sure your Fund definition has valid definitions of:
    
* withdrawal custody policy
* transfer custody policy
* at least one Wallet

It is not required for the Wallet definition to have custom custodial policies. 

#### Wallet definition object 

You can add more Wallets to your Fund at a later stage using `POST /company/{company_id}/fund/{fund_ID}/wallet`.

```json
{
  "name": "New Wallet with custom custody group",
  "asset": "BTC-TESTNET",
  "custodygroup_withdraw": {
  "threshold": 1,
  "members": [ list of withdrawal approvers
  ]
  },
  "custodygroup_tx": {
  "threshold": 1,
  "members": [ list of tx approvers
  ]
  }
}
```

If the Wallet definition doesn't include a custodial policy, it automatically inherits the corresponding one of the Fund. You are not required to add custom (per Wallet) custodial policies.

<<<<<<< HEAD
A Fund can have multiple Wallets. It is up to you to define a structure that suits your purposes and use cases.

{% callout type="warning" title="Fund and Wallets created in Partner API not visible in Qredo Web App" %}

Similar to the Company and trusted parties, the Funds (and Wallets) you create in the API will not be visible in the Web App.

{% /callout %}

### Example request: `POST /fund`

To create your Fund, you must use `POST /fund`. Along with a Fund name, you will add custodial policies and Wallets to your Fund.

This example creates a Fund named "Awesome Fund" that contains the following definitions:

* a `custodygroup_withdraw` policy with `threshold` set to `2` and 3 specified trusted network `members`
* a `custodygroup_tx` policy with `threshold` set to `3` and 4 specified trusted network `members`
* two `Wallets`: one with global custodial policies and one with custom custodial policies

With this API resource, you pass [`company_id`](/developer-guides/partner-api/walkthrough#step-1-create-a-company) as a URL parameter.

```bash
curl -X POST "https://api.qredo.network/api/v1/p/company/{company_id}/fund" 
```

#### Request body

The following top-level parameters are required: `name`, `description`, `custodygroup_withdraw`, `custodygroup_tx`. You can add more `Wallets` at a later stage but, once created, you cannot remove Wallets.

```json
{
  "name": "Awesome fund",
  "description": "A demo fund",
  "custodygroup_withdraw": {
    "threshold": 3,
    "members": [
      "<withdrawal trusted party01 id>",
      "<withdrawal trusted party02 id>",
      "<withdrawal trusted party03 id>"
    ]
  },
  "custodygroup_tx": {
    "threshold": 2,
    "members": [
      "<tx trusted party 01 id>",
      "<tx trusted party 02 id>",
      "<tx trusted party 03 id>",
      "<tx trusted party 04 id>"
    ]
  },
  "wallets": [
    {
      "name": "Demo ETH-TESTNET Wallet that uses global custodial policies",
      "asset": "<asset type: BTC, ETH, etc.>",
    },
    {
      "name": "Demo ETH-TESTNET Wallet with custom custodial policies",
      "asset": "<asset type: BTC, ETH, etc.>",
      "custodygroup_withdraw": {
        "threshold": 2,
        "members": [
          "<withdrawal trusted party01 id>",
          "<withdrawal trusted party02 id>"
        ]
      },
      "custodygroup_tx": {
        "threshold": 1,
        "members": [
          "<tx trusted party 01 id>",
          "<tx trusted party 02 id>"
        ]
      }
    }
  ]
}
```

{% callout type="warning" title="You cannot update the Fund's custodial policies!" %}

Once you create a Fund, you cannot update the custodial policies. You can update the custodial policies of a Wallet.

{% /callout %}

#### Custodial policies explained

The `custodygroup_withdraw` on top level is the global (per Fund) withdrawal custodial policy definition. The `custodygroup_withdraw`, part of the "Demo ETH-TESTNET Wallet with custom custodial policies" is the definition the overriding policy.

It is the same with the global vs. custom transfer policies.

Both with transfers and withdrawals, a custody policy includes:

* `threshold`: this value defines how many approvals are required for a transaction to go through. In this case it is set to `2`
* `members`: this list includes IDs of members of your trusted network. In this case, three members are listed and at least two approvals are required

Obviously, for transactions to pass through custody, the `threshold` value must be less than or equal to the number of specified `members`.

The `custodygroup_tx` is configured similarly to `custodygroup_withdraw` and in this case is set to require 3 approvals (`threshold` set to `3`) from three of the four specified `members`.

{% callout type="warning" title="Always define custodial policies per Fund!" %}

If you create a Fund without custodial policies and you add Wallets without custodial policies, transactions using these addresses may get stuck in the custodial approval process.

{% /callout %}

Definitions of custom (per Wallet) custodial policies have the exact same structure.

#### Wallet definitions explained

* A Wallet on the Qredochain is not merely an address per asset type: it is rather a definition, that may or may not include custodial policies and dedicated approvers.
* A Wallet on the Qredochain always belongs to the Fund it was created in: with Qredo, there are no Wallets outside a Fund.

The `wallets` object lists two Wallets: one that uses the global custodial policies (has no custom policy definitions) and another that uses custom ones.

### Example response: `POST /fund`

Upon success, the response returns the `fund_id` hash as a string value plus policies, assets, trusted network members and Wallets.

```json
{
  "fund id": "<ID of your Fund>",
  "name": "<name of your Fund>",
  "description": "<description of your Fund>",
  "custodygroup withdraw": "<Fund custodial policy ID for withdrawals>",
  "custodygroup tx": "<Fund custodial policy ID for transactions>",
  "policy withdraw": "<object containing withdraw policy info>",
  "policy tx": "<object containing withdraw policy info>",
  "members": "<list of Fund members and details of each>",
  "assets": "<list of supported assets in your Fund>",
  "wallets": "<list of Wallet objects with info like addresses and balances>"
}
```

The response, as shown, is significantly shortened: objects are presented as "collapsed". This is intentional and aims to keep things simple. 

{% callout type="note" title="Pass `fund_id` with deposits, withdrawals, Wallets, ledger, etc." %}

Save the `fund_id` value to perform different Fund-specific actions, such as deposit and withdrawal transactions, creation of additional Wallets and retrieving ledger tx history. Transfer transactions do not require `fund_id`.

{% /callout %}

{% callout type="note" title="For brevity, Wallets omitted from response" %}

The above response contains the Wallet definitions as well. However, Wallet definitions with custom custodial policies also contain the definitions for these policies. For the sake of brevity Wallets will not be discussed in detail just yet.

{% /callout %}

### Optional step: Create a Wallet

While it is not a necessary step to complete this tutorial, you should know more about how to create and use your Wallets.

#### Example request `POST /wallet`

This example creates a single Wallet in a Fund and appoints two custodians for both transfer and withdrawal transactions. The approval threshold is 1, which means one approval is enough for a transaction out your Wallet. A successful response returns the **Wallet ID**.

With this API resource, you pass [`company_id`](/developer-guides/partner-api/walkthrough#step-1-create-a-company) and [`fund_id`](/developer-guides/partner-api/walkthrough#step-3-create-a-fund) as URL parameters:

```
curl -X 'POST' 'https://api.qredo.network/api/v1/p/company/{company_id}/fund/{fund_id}/wallet'
```

##### Request body

You can create any type of Wallet, depending on what you specify in the request body.


{% tabs %}
{% tab label="New wallet" %}
For this Wallet custody policies are not specified. It inherits them from the Fund.

``` json
{
  "wallets": [
    {
      "name": "New wallet",
      "asset": "BTC-TESTNET",
    }
  ]
}
```
{% /tab %}
{% tab label="New wallet with custody policies" %}
This Wallet body has custody policies configured: see the `custodygroup_withdraw` and `custodygroup_tx` objects.

``` json
{
  "wallets": [
    {
      "name": "BTC-Testnet Wallet",
      "asset": "BTC-TESTNET",
      "custodygroup_withdraw": {
        "threshold": 1,
        "members": [
          "13DCSmrasFk84U7Q95aSpY3eBYcC6s3CGuJXf4AvDiag",
          "6kC7CmoFavDQKct5qwTeY4yGQCUFcPcEdRWpcLpVuz4W"
        ]
      },
      "custodygroup_tx": {
        "threshold": 1,
        "members": [
          "13DCSmrasFk84U7Q95aSpY3eBYcC6s3CGuJXf4AvDiag",
          "6kC7CmoFavDQKct5qwTeY4yGQCUFcPcEdRWpcLpVuz4W"
        ]
      }
    }
  ]
}
```
{% /tab %}
{% tab label="New dedicated wallet" %}
This is a request body for adding a dedicated Wallet. The `counterparty_id` and `client_id` values in the `connect` object define the Counterparty you're connecting to.

``` json
{
  "wallets": [
    {
      "name": "BTC-Testnet Wallet",
      "asset": "BTC-TESTNET",
      "custodygroup_withdraw": {
        "threshold": 1,
        "members": [
          "13DCSmrasFk84U7Q95aSpY3eBYcC6s3CGuJXf4AvDiag",
          "6kC7CmoFavDQKct5qwTeY4yGQCUFcPcEdRWpcLpVuz4W"
        ]
      },
      "custodygroup_tx": {
        "threshold": 1,
        "members": [
          "13DCSmrasFk84U7Q95aSpY3eBYcC6s3CGuJXf4AvDiag",
          "6kC7CmoFavDQKct5qwTeY4yGQCUFcPcEdRWpcLpVuz4W"
        ]
      }
    }
  ]
}
```
{% /tab %}
{% /tabs %}

{% callout type="note" title="you can create several wallets at once"%}
You can create several Wallets at once: just pass multiple objects in the `wallets` array. 
{% /callout %}


#### Example response `POST /wallet`

A successful response returns the `wallet_id`.

```
{"wallets":["3Hqis3UuRGTvJVQ7AxCj9nesDBydPKWfZge6hj31sRop"]}
```

{% callout type="warning" title="Wallet ID vs Wallet address" %}

Wallet ID is pertinent to Qredo network only. Do not mistake this ID with the Wallet address. In terms of Qredo, a Wallet address is used only when moving assets from L1 blockchain to Qredochain (deposits), or vice versa: from Qredochain to L1 blockchain (withdrawals). Only *deposit Wallets* have addresses. In all API calls for moving assets within the Qredo Network, or query Wallet details, use this `wallet_id` parameter.

{% /callout %}

### Optional step: Query for Wallet details

You would not always need to retrieve Wallet details but it certainly might come in handy. 

#### Example request `GET /wallet/{wallet_id}`

This example fetches details of a Wallet, identified by its `wallet_id`.

#### Example response `GET /wallet/{wallet_id}`

The response returns very detailed info about this Wallet, including `address` and `balance`. In fact, the response is a bit large, especially with custom trusted policies and multiple trusted parties.

``` json
{
    "wallet_id": "3Hqis...1sRop",
    "name": "BTC-Testnet wallet",
    "asset": "BTC-TESTNET",
    "type": 0,
    "status": "ready",
    "address": "mm1Bvy1iXQUJA1eh1edhoDG4yGzdo583iE",
    "address_type": "P2PKH",
    "short_code": "trophy turtle metal",
    "balance": 0,
    "custodygroup_withdraw": "21cDwa2ap1jUytjwdtyrgaRxZ74",
    "custodygroup_tx": "21cDwSnWn8VzSsfKVU9UbSxXDeL",
    "policy_withdraw": {
        "id": "21cDwa2ap1jUytjwdtyrgaRxZ74",
        "entityID": "",
        "name": "Wallet BTC-Testnet wallet Withdraw policy",
        "locked": false,
        "lockedTxID": "",
        "members": [
            {
                "dedicated": false,
                "entity": {
                    "id": "13DCS...vDiag",
                    "accountCode": "2VvnB...wH41h",
                    "name": "Trent Kryptor",
                    "initials": "TK",
                    "type": "user",
                    "user": {
                        "firstName": "Trent",
                        "lastName": "Kryptor",
                        "email": "toma@qredo.com",
                        "username": "apiSign"
                    }
                }
            },
            {
                "dedicated": false,
                "entity": {
                    "id": "9niR8...RW3xu",
                    "accountCode": "9niR8...RW3xu",
                    "name": "Demo Core Client",
                    "initials": "C",
                    "type": "core-client",
                    "coreClient": {
                        "name": "Demo Core Client"
                    }
                }
            }
        ],
        "threshold": 1,
        "membersCount": 2,
        "walletCount": 0,
        "dedicatedWalletCount": 0,
        "fundsCount": 0,
        "funds": null
    },
    "policy_tx": {
        "id": "21cDwSnWn8VzSsfKVU9UbSxXDeL",
        "entityID": "",
        "name": "Wallet BTC-Testnet wallet Tx policy",
        "locked": false,
        "lockedTxID": "",
        "members": [
            {
                "dedicated": false,
                "entity": {
                    "id": "13DCS...vDiag",
                    "accountCode": "2VvnB...wH41h",
                    "name": "Trent Kryptor",
                    "initials": "TK",
                    "type": "user",
                    "user": {
                        "firstName": "Trent",
                        "lastName": "Kryptor",
                        "email": "toma@qredo.com",
                        "username": "apiSign"
                    }
                }
            },
            {
                "dedicated": false,
                "entity": {
                    "id": "9niR8...RW3xu",
                    "accountCode": "9niR8...RW3xu",
                    "name": "Demo Core Client",
                    "initials": "C",
                    "type": "core-client",
                    "coreClient": {
                        "name": "Demo Core Client"
                    }
                }
            }
        ],
        "threshold": 1,
        "membersCount": 2,
        "walletCount": 0,
        "dedicatedWalletCount": 0,
        "fundsCount": 0,
        "funds": null
    }
}
```

## Step 4: Perform a deposit

A *deposit* on the Qredo network is a transaction that transfers Funds from a L1 blockchain address to a Qredochain (L2) address. You perform a deposit to a Wallet in your Fund using external Wallet services such as MetaMask, Exodus, Electrum, etc.

Now that you have [a Fund](/developer-guides/partner-api/walkthrough#step-3-create-a-fund) with one or more Wallets, you can deposits assets.

{% callout type="note" title="Transaction types in this walkthrough" %}

The transactions you will perform in this walkthrough are:

* deposit - transfer assets from a Level 1 Wallet (outside Qredochain) to a Qredo Wallet: L1 to L2 transaction
* transfer - transfer assets from one Qredo Wallet to another Qredo Wallet: L2 to L2 transaction 
* withdrawal - transfer assets from a Qredo Wallet to a L1 Wallet outside Qredochain: L2 to L1 transaction

Don't forget that approvers *do not* approve deposit transactions.

{% /callout %}

To complete this step, you must:

1. Use the `GET /deposit` resource in this example to fetch Wallet information such as *address*, *asset type* and current (initial) *balance* of each. Obviously, the balance of a newly created Wallet equals `0`.
2. Perform a deposit transfer from an external Wallet (Metamask, Exodus, Electrum, or an exchange, etc.).
3. Use the `GET /deposit` resource again to verify that your Wallet balance has reflected this deposit transaction on the Qredo network.

### 1. Retrieve address for deposit

Each Fund contains one or more deposit *addresses* for the different types of *assets* and you must make sure to get the right ones before you proceed.

!!! Important "Use Wallet L1 **address** for deposits"
    From a Qredo standpoint, your L2 Wallet has an ID and a L1 address. To deposit assets from a L1 Wallet, you must always identify the deposit Wallet on Qredo using its L1 address.

#### Example request: `GET /deposit`

This example retrieves details of all *deposit* Wallets in a Fund. With this API resource, you pass [`company_id`](/developer-guides/partner-api/walkthrough#step-1-create-a-company) and [`fund_id`](/developer-guides/partner-api/walkthrough#step-3-create-a-fund) as URL parameters.

```bash
curl -X GET "https://api.qredo.network/api/v1/p/company/{company_id}/fund/{fund id}/deposit" 
```

No body parameters are sent with this request.

#### Example response: `GET /deposit`

The response returns a list with all deposit Wallet addresses.

```json
{
  "total_count": 1,
  "list": [
    {
      "asset": "<L1 asset type: `BTC`, `ETH`, ETH-TESTNET, BTC-TESTNET, etc",
      "address": "<deposit Wallet address>",
      "balance": "<deposit Wallet balance in base unit per 'asset': satoshis, gweis, etc.>"
    }
  ]
}
```

### 2. Make your deposit using external Wallet

Use your preferred external Wallet service to deposit assets from a Level 1 Wallet into your Qredo Wallet address you just retrieved. Note that this guide doesn't include instructions on services external to Qredo.

### 3. Verify deposit success

When you submit the deposit amount, make sure the balance in the Qredo deposit Wallet reflects this transaction. Use `GET /deposit` once again to ensure the response returns the deposit Wallet balance updated with your deposit amount.

Once your balance reflects the transaction, you can proceed with the next step.

{% callout type="note" title="`balance` returned in base units per asset type" %}
The value of your `balance` is returned denominated in smallest base units per asset: for example, 100000000 *satoshis* equals 1 BTC (scale of 100000000) and 1000000000 *gweis* with ETH (scale of 1000000000).
{% /callout %}

## Step 5: Perform a transfer

With Qredo, a *transfer* transaction means to move assets from one Qredo Wallet (originating) ID to another Qredo Wallet (destination) ID. Note that when we talk a transfer on the Qredochain, this is a L2 to L2 transaction and the Wallets are identified by their IDs and not their Wallet addresses. Naturally, the transfer flow means you submit a transfer request which will remain in pending status until custody approval / rejection or expiry.

{% callout type="note" title="Use Wallet IDs for transfers" %}

    From a Qredo standpoint, L2 Wallets on both sides of a transfer transactions are always identified by their Wallet IDs and never by their L1 addresses.

{% /callout %}

From an approver's standpoint, the transfer transaction is also called a *transfer out* transaction.

To complete this step, you must:

1. Use `GET /fund` to retrieve your Qredo Wallet ID.
2. Use `POST /transfer` to submit transfer request.
3. Wait for custodial approval to take place.
4. Use `GET /transfer/{tx_id}` to verify that the transaction is approved.

### 1. Retrieve Wallet ID

This example retrieves detailed information about the Fund, including the custodial policies, list of trusted network members, and all Wallets that belong to the Fund.

#### Example request: `GET /fund`

With this request, you pass `company_id` and the `fund_id` as URL parameters.

```bash
curl -X GET "https://play-api.qredo.network/api/v1/p/company/{company_id}/fund/{fund_id}"
```

No body parameters are sent with this request.

#### Example response: `GET /fund`

The response returns a large object with Fund-specific data. For sake of brevity, objects and lists are collapsed in the example.

```json
{
  "fund_id": "<ID of your Fund>",
  "name": "<name of your Fund>",
  "description": "<description of your Fund>",
  "custodygroup withdraw": "<Fund custodial policy ID for withdrawals>",
  "custodygroup tx": "<Fund custodial policy ID for transactions>",
  "policy withdraw": "<object containing Fund withdraw policy info>",  
  "policy tx": "<object containing Fund withdraw policy info>",
  "members": "<list of Fund trusted members and details of each>",
  "assets": "<list of supported assets in your Fund>",
  "wallets": [
    {
      "wallet_id": "<ID of Wallet>",
      "name": "<Wallet name>",
      "asset": "BTC-TESTNET",
      "type": 0,
      "status": "ready",
      "address": "n3L2rkVup9xGbCfP4rnoJZJF5MqJFAiBS1",
      "address_type": "P2PKH",
      "short_code": "<firstword secondword thirdword>",
      "balance": 10000,
      "custodygroup_withdraw": "<Wallet custodial policy ID for withdrawals>",
      "custodygroup_tx": "<Wallet custodial policy ID for transactions>",
      "policy_withdraw": "<object containing Wallet withdraw policy info>",
      "policy_tx": "<object containing Wallet withdraw policy info>"
    }
  ]
}
```

Note that a Wallet always holds one asset type. In this example, there are 10000 satoshis worth of BTC-TESTNET available in the Wallet.

{% callout type="note" title="Shortcodes vs. Wallet IDs" %}

Wallets on the Qredo Network have `short_code` values which are interchangeable with Wallet IDs. The "short code" is a combination of three unique English words separated by space intervals, in the format: 

`"short_code": "firstword secondword thirdword"`. 

To view the short code of a Wallet, you can either call `GET fund/{fund_id}` or `GET wallet/{wallet_ID}`.

{% /callout %}

This example doesn't provide a specific combination of shortcode words as it may turn out to correspond to an actual Wallet on the Qredo Network.

### 2. Submit a transfer request

This example submits a request for transferring assets from one Qredo Wallet to another.

#### Example request: `POST /transfer`

This example submits a transfer request. With this API resource, you pass [`company_id`](/developer-guides/partner-api/walkthrough#step-1-create-a-company) as a URL parameter.

```bash
curl POST "https://api.qredo.network/api/v1/p/company/{company_id}/transfer"
```

##### Request body

All described fields are required. Note the distinction the originating Wallet is specified by `wallet_id` while the destination Wallet is defined by `counterparty_wallet_address`.

```json
{
  "wallet_id": "<Wallet ID of origin Wallet: not the address>",
  "counterparty_wallet_address": "<Wallet ID of destination Wallet: not the address>",
  "expires": "<Epoch timestamp when transfer request expires if not approved>",
  "benefit_of": "<beneficiary name",
  "account_no": "<account number: a custom field>",
  "reference": "<reference value: a custom field>",
  "partner_txID": "<a transaction ID: a custom field>",
  "send": {
    "symbol": "<asset symbol (BTC, ETH, etc.)>",
    "amount": "<asset amount (satoshis, gweis, etc.)>"
  }
}
```

{% callout type="note" title="Use shortcodes instead of both Wallet IDs" %}

As a reminder, note that with transactions, you can use short codes instead of:

* `wallet_id` - interchangeable with `short_code` value of origin wallet.
* `counterparty_wallet_address` - interchangeable with `short_code` value of destination Wallet.

{% /callout %}

##### TX request body: use  `partner_txID` to prevent duplicate transactions

The `partner_txID` is a custom parameter, and is used as a separate unique identifier of this transaction. This might be an ID with a system external to Qredo, that you want to map the current transaction to, so its value is coupled with the specific `tx_id`. This allows you to:

* map a `tx_id` to another identifier which is unique in whatever way you choose.
* prevent you from making a *duplicate* transaction with the same `partner_txID`. If you try to re-use this ID, the response of `POST /transfer` you will return the `tx_id` value of the original transaction with that `partner_txID` and with `"status": "existing"`.

{% comment %}

??? danger "Summary: use `partner_txID` to prevent duplicate transactions"

    The `partner_txIDs` is a custom parameter, an external a unique identifier of this transaction that you can map the current transaction to. Qredo stores its value and will prevent you from making a *duplicate* transaction: one with the same `partner_txID`.

{% /comment %}

#### Example response: `POST /transfer`

A successful response returns the *transaction ID* and transfer request *status*.

```json
{
  "tx_id": "<transaction ID>",
  "status": "new"
}
```

{% callout type="note" title="View tx status using `tx_id`" %}

From this point on, completion of the transfer is pending custodial actions. Save the `tx_id` to view the transaction status. The Partner API offers several API calls that pass the `tx_id` of transactions.

{% /callout %}

### 3. Undergo custodial approval

The custodial approval process is outside the scope of this tutorial. We assume the transfer transaction is approved in order for this step to be completed.

### 4. View transaction status

As in a regular case you might not know when the custodial process is complete, you can check the transaction status using the `GET /transfer/{tx_id}` API resource.

#### Example request: `GET /transfer/{tx_id}`

This example retrieves transfer transaction info. With this API resource, you pass [`company_id`](/developer-guides/partner-api/walkthrough#step-1-create-a-company) and the transfer `tx_id` as URL parameters.

```bash
curl -X GET /company/{company_id}/transfer/{tx_id}
```

No body parameters are sent with this request.

#### Example response: `GET /transfer/{tx_id}`

A successful response returns an object containing transaction-specific data.

```json
{
  "approvedCount": "<number of custody approvals>",
  "expires": "<Epoch timestamp when tx request expires if not approved>",
  "initiatedTimestamp": "<Epoch timestamp when tx request is initiated>",
  "initiatorAuthorized": "<a flag if tx underwent custody: 'true' or 'false'>",
  "initiatorName": "<name of initiator company>",
  "reference": "<your reference>",
  "status": [
    {
      "actionID": "<action ID>",
      "firstName": "<approver first name>",
      "lastName": "<approver first name>",
      "status": "<approval status with current approver: e.g. 'approved'>",
      "timestamp": "<Epoch time when custody action took place>",
      "userID": "<approver user ID>", 
      "username": "<approver Qredo account>"
    }
  ],
  "statusDetails": {
    "accountNo": "123-XX",
    "amount": "<tx amount in base units>",
    "asset": "<asset symbol: BTC, ETH, etc.>",
    "benefitOf": "<recipient full name>",
    "expires": 0,
    "fees": 1,
    "fundID": "<Fund ID>",
    "netAmount": 800,
    "recipientAddress": "<destination Wallet ID>",
    "recipientEmail": "<destination account email address>",
    "recipientFirstName": "<recipient first name>",
    "recipientLastName": "<recipient last name>",
    "recipientUsername": "<recipient Qredo account>",
    "reference": "CX15R99XX",
    "requestedBy": "Rosa Parks" 
  },
  "thresholdRequired": "<number of required custody approvals>",
  "txID": "<transaction id>",
  "txStatus": "<tx status: pending, approved, rejected, expired>" 
}
```

{% callout type="note" title="Details on some tx-specific objects" %}

* `approvedCount`: the number of approvals. With approved transactions this value is the same as the value of `thresholdRequired`.
* `status`: an object with user data about each approver of the transaction, including their `userID`, and `username`, etc. Obviously, the number of status objects returned must correspond to the `approvedCount` value.
    * With *Core Client* transactions, `firstName`, `lastName` and `username` fields under `status` are blank and `userID` is the *Core Client ID*.
    
* `statusDetails`: details including fees incurred, the net amount transferred, and recipient / beneficiary info, etc.

{% /callout %}

Once approved, the transfer is written to the Qredo Blockchain.

## Step 6: Perform a withdrawal

With Qredo, a *withdrawal* transaction means moving assets from a *Wallet ID* to an *external Wallet address*: one that is not on the Qredochain. 

To complete this step, you must:

1. Use the `POST /whitelist` resource to make a L1 address eligible for withdrawing assets outside the Qredochain.
2. Use the `POST /withdraw` resource to submit a withdrawal request (to the whitelisted address) for approval.
3. Wait for custodial approval to take place.
4. Use the `GET /withdraw` resource to verify that the transaction is completed.

### 1. Whitelist a withdrawal address

This example whitelists a counterparty address that is external to the Qredochain and specifies its asset type. For withdrawals, you must always whitelist the destination external address. Note that you can have a single address whitelisted for more than one Fund in a Company.

#### Example request: `POST /whitelist`

With this request, you pass [`company_id`](/developer-guides/partner-api/walkthrough#step-1-create-a-company) and the [`fund_id`](/developer-guides/partner-api/walkthrough#step-3-create-a-fund) as URL parameters.

```bash
curl -X POST /company/{company_id}/fund/{fund_id}/whitelist
```

##### Request body

All described fields are required.

```json
{
  "name": "<memorable name for counterparty address>",
  "asset": "<asset symbol>", 
  "address": "<external counterparty address>"
}
```

#### Example whitelist response: `POST /whitelist`

A successful response returns a `HTTP 200 OK` confirmation. You can now use the whitelisted address for withdrawals.

### 2. Submit withdrawal request

Now that you have a whitelisted external Wallet, you can submit the withdrawal request.

{% callout type="note" title="Use destination Wallet L1 **address** for withdrawals" %}

From a Qredo standpoint, when you withdraw assets to Level 1 blockchain, you will define the a L1 Wallet address. To deposit assets from a L1 Wallet, you must always identify the deposit Wallet using its L1 address.

{% /callout %}

#### Example withdrawal request: `POST /withdraw`

This example submits for approval a withdrawal request to a whitelisted Wallet that is outside the Qredochain.

```bash
POST /company/{company_id}/withdraw
```

##### Request body

All described fields are required.

```json
{
  "wallet_id": "<Wallet ID of origin Wallet: not the address>",
  "address": "<whitelisted Wallet address>",
  "expires": "<Epoch timestamp when withdrawal request expires if not approved>",,
  "benefit_of": "<names of beneficiary: a custom field>",
  "account_no": "<account number: a custom field>",
  "reference": "<reference value: a custom field>",
  "partner_txID": "<a transaction ID: a custom field>",
  "send": {
    "symbol": "<asset symbol (BTC, ETH, etc.)>",
    "amount": "<asset amount (satoshis, gweis, etc.)>"
  }
}
```

#### Example withdrawal response: `POST /withdraw`

A successful response returns a `tx_id`. You will use this value when you need to [view the withdrawal status](/developer-guides/partner-api/walkthrough#step-6-perform-a-withdrawal).

```json
{
  "tx_id": "<withdrawal transaction ID>",
  "status": "new"
}
```

### 3. Undergo custodial approval

The custodial approval process is outside the scope of this tutorial. Let's assume the withdrawal request is approved in order for this step to be completed.

### 4. View withdrawal status

As in a regular case you might not know when the custodial process is complete, you can check the withdrawal status using the `GET /withdraw/{tx_id}` API resource.

#### Example request: `GET /withdraw/{tx_id}`

This example retrieves withdrawal transaction info. With this API resource, you pass [`company_id`](/developer-guides/partner-api/walkthrough#step-1-create-a-company) and the withdrawal `tx_id` as URL parameters.

```bash
curl -X GET /company/{company_id}/withdraw/{tx_id}
```

No body parameters are sent with this request.

#### Example response: `GET /withdraw/{tx_id}`

The response structure with `GET /withdraw/{tx_id}` is the same as with `GET /transaction/{tx_id}`, discussed in [Step 5: Perform a transfer](/developer-guides/partner-api/walkthrough#4-view-transaction-status). To avoid repetition, we'll not go into further detail.

{% callout type="note" title="This was the final step in this walkthrough" %}

This was the final step in this walkthrough tutorial. By now, you should be familiar with all basic concepts of the Partner API and can move on with some of the more complex concepts, like start using the Core Client.

{% /callout %}




