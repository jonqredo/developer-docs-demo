---
comment: Moved SA GitHub to here
title: Get started with Signing Agent
author: HB and Signing Agent team's docs
proofedDate: 20221217
---

## TL;DR

To integrate your App with the Signing Agent and Partner API:

- Set up API keys for [Partner API](/developer-guides/partner-api/get-started)
- Run the [docker image](docker) for the Signing Agent
- [Configure](configure) your implementation
- [Create a Signing Agent](docker#register-your-signing-agent) and collect the unique ID, `agentID`
- [Add `agentID`/Signing Agent's name](implement) to the entity and policies required


## Integration overview

The Signing Agent is a standalone component of the Qredo ecosystem. To run a Signing Agent, first you need to register it on the Qredo network. A new Signing Agent is created via the Partner API. Each Signing Agent receives a unique identifier. This ID can be added to the 'members' group and included in the custody policy:`custodygroup_withdraw` and/or the `custodygroup_tx` for an entity.

The following diagram demonstrates the basic flow with regards to:

- *PartnerApp* (i.e. your App)
- *Signing-Agent-Service* (e.g. Signing Agent running on your infrastructure)
- and *QredoBE* (backend).

{% mermaid %}
sequenceDiagram
PartnerApp->>signing-agent-service:POST /register {"name":"...","APIKey":"...","Base64PrivateKey":"..."}
signing-agent-service->>signing-agent-service: Generate BLS and EC Keys
signing-agent-service->>QredoBE: Register agent with Qredo Chain
QredoBE->>signing-agent-service: {agentID, feedURL, other data}
signing-agent-service->>PartnerApp: {agentID, feedURL}
{% /mermaid %}


### Step 1: Initiate integration with Partner API


**1. Sign up to use the [Partner API](/developer-guides/partner-api/get-started).**

You will create a Qredo Account and enable Partner API access.

**2. Generate the [API keys](/developer-guides/partner-api/generate-keys).**

Three major outcomes must be achieved:

- [Generate the API keys within the Web App for authentication](/developer-guides/partner-api/generate-keys#api-keys-for-authentication)
- [Generate RSA keys for authorization](/developer-guides/partner-api/generate-keys#rsa-keys-for-signing)
- [Notify Qredo of the public RSA key via the Web App](/developer-guides/partner-api/generate-keys#upload-public-rsa-key-to-qredo-web-app)


**3. Set up [testnet assets](/user-guides/testnet)** (optional/recommended)

This is optional for the Production Web App and Production environment. Testnet assets must be activated for use in the Sandbox environment, [https://sandbox.qredo.network/]( https://sandbox.qredo.network).

{% callout type="note" title="Return Here" %}
Carry out the Steps above and return here to continue the flow.
{% /callout %}


### Step 2: [Deploy Signing Agent](docker)

Next, you will work with the [Docker image](docker) and Signing Agent [configuration file](configure) to instigate a self-served Rest API service to register your Signing Agent with Partner API.

The self-served Rest API generates an agent ID which may be added alongside, or in place of, trusted parties in your custody policy.

{% callout type="note" title="Register a Signing Agent" %}
Setting up your Docker image is covered in a [dedicated page](docker). Once you register a Signing Agent and hold the agentID, you are ready to implement that Signing Agent as part of your custodial policies. The final part of the flow, [implement](implement) ensures you have the entities in place to support such policies.
{% /callout %}

### Step 3: [Implement your Signing Agent](implement)

{% callout type="note" title="Ensure you have the required entities" %}

A Signing Agent becomes part of your custody group according to your policy requirements. This means that to implement Signing Agent, you must have the governing entity set up and determine which level you wish to apply custody policies to.

{% table %}
* 
* [Partner API](/developer-guides/partner-api/get-started)
* [Qredo API v1](/api-reference/qredo-api)
---
* **Entity Name**
* Company
* Organization
---
* **Supported policy level**
* Company
* Organization
---
*
* Fund
* Fund
---
* 
* Wallet
* Wallet
{% /table %}
{% /callout %}

{% comment %} Danger: Duplicated from concepts/approvals.md {% /comment %}

## Registration flow

The following explanation summarizes the registration process, which involves the *PartnerApp* (i.e. your 3rd-party App), the *signing-agent-service* (e.g. Signing Agent running on your infrastructure), and *QredoBE* (e.g. our Qredo backend).


{% mermaid %}
sequenceDiagram
participant PA as 3rd-party App PartnerApp
participant AA as 3rd-party App Signing Agent
participant Q as QredoBE
autonumber
  PA->>AA: POST /register {"name":"...","APIKey":"...","Base64PrivateKey":"..."}
  AA->>AA: Generate BLS and EC keys
  AA->>AA: {BLSPublic, ECPublic, RefID}
  AA->>Q: POST /p/client/init Body: {BLS & EC Public Keys}
  Q->>Q: Create new MFA ID, New IDdoc
  Q->>AA: {ID, AccountCode, ClientID, ClientSecret, unsigned IDdoc}
  AA->>AA: Store ID, ClientSecret, ClientID, AccountCode
  AA->>AA: Sign IDDoc
  AA->>Q: POST /p/client/finish { IDdoc Signature }
  Q->>Q: Save IDdoc in Qredochain
  Q->>AA: {feedURL}
  AA->>PA: {feedURL, agentID}
{% /mermaid %}

1. The *PartnerApp* triggers the registration process by providing its client name, Partner API Key, and Base64-Private Key to the *signing-agent-service*.
2. *signing-agent-service* generates BLS and EC keys.
3. *signing-agent-service* stores BLS and EC keys.
4. The *signing-agent-service* can now register itself to the Partner API on the *QredoBE*, by sending the `client name`, `BLS`, and `EC` public keys.
11. The `agentId` and a `feedURL` is returned by the *QredoBE* to the *signing-agent-service*. This feed is used by the *signing-agent-service* to keep a communication channel open with the *QredoBE*.
12. The `agentId` and a `feedURL` is also passed along to the *PartnerApp* so that the latter can monitor for new actions that need to be approved in that case that the service is not configured for auto-approval.


All the data above is currently stored on premises in a file by the signing-agent-service, and since some of it (ClientSecret, EC & BLS private keys) is quite sensitive it needs to be running in a secure environment.
