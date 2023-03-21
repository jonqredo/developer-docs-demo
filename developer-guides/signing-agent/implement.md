---
comment: wip PAPI has Signing App user approval stage, not clear if this is genuine. If it is, does Signing Agent have the same? IF NOT needs to be removed from pAPI docs walkthrough
title: Implement Signing Agent
author: HB and Signing Agent team's docs
proofedDate: 20221217
---

## TL;DR

- A Signing Agent is identified by `agentID` 
- Add your Signing Agent to the governing entity's trusted network
- Add your Signing Agent to a custody policy

{% comment %} todo need to distinguish between adding this to an existing policy vs a new policy NOTE the current flow does not actually take user to the point of adding to policy -- it adds user to the "company" as per the wording in pAPI {% /comment %}

## Implement your Signing Agent

### Step 1: Add the Signing Agent as a trusted party

{% tabs %}
{% tab label="Partner API" %}
1. Identify the `company_id` for the Company that owns the custody policy you wish to add the Signing Agent to. 

2. Add your Signing Agent's `agentID` to that Company.  
  
Follow the [guide](/developer-guides/partner-api/walkthrough#step-2-add-a-trusted-party) or the API specification for [POST /trustedparty](https://developers.qredo.com/partner-api/api/swagger/#tag/TrustedNetwork/paths/~1company~1%7Bcompany_id%7D~1trustedparty/post).
{% /tab %}
{% tab label="Qredo API" %}
Follow the [UI guide](/user-guides/trusted-network) to add the Signing Agent to your Organization's trusted network.
{% /tab %}
{% /tabs %}

### Step 2: Add the Signing Agent to a policy

{% tabs %}
{% tab label="Partner API" %}
There are two possible scenarios when adding a Signing Agent.

#### Option 1: Create a new Fund **and** specify the trusted parties in its policy.

[Follow the guide](/developer-guides/partner-api/walkthrough/03-create-fund) or the API specification for [`POST /company/{company_id}/fund`](https://developers.qredo.com/partner-api/api/swagger/#tag/Fund/paths/~1company~1%7Bcompany_id%7D~1fund/post)

#### Option 2: Update the policy of an existing Wallet.

Follow the API specification for [`POST /company/{company_id}/fund/{fund_id}/wallet/{wallet_id}/policy`](https://developers.qredo.com/partner-api/api/swagger/#tag/Wallet/operation/update-wallet-policy)
{% /tab %}
{% tab label="Qredo API" %}
#### Option 1: Update an existing policy to include the Signing Agent

[Follow the UI guide](/user-guides/policies#update-an-existing-policy) using your Signing Agent user-assigned name or the `agentID`.

#### Option 2: Add a new Fund or Wallet and create a policy that includes your Signing Agent.

[Follow the UI guide](/user-guides/policies#create-a-new-policy) using your Signing Agent user-assigned name.
{% /tab %}
{% /tabs %}

