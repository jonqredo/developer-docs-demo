---
title: Introduction
comment: to do load balancer instructions
author: HB
proofDate: 20021217
---


## TL;DR

- Automate transaction signing
- Avoid approver fatigue


## Service overview

Signing Agent enables you to automate the [approval](/glossary/approvals) or rejection of [transactions](/glossary/transactions) according to your customized rulesets for dedicated connected Wallets.

Signing Agent becomes a programmatic member of your custody group: speeding up the approval process and relieving the demand on trusted parties who make approvals through the Qredo Signing App on their mobile. From the perspective of your custody policies, an automated approval by a Signing Agent equates to one approval by a (human) trusted party. It can be used to create a programmable approver service that listens to incoming approval requests over WebSockets to perform automated custody.

Signing Agent uses a dedicated subset of the Partner API for fetching, approving, and rejecting transactions pending custody. It can operate as a standalone service (exposing a RESTful API to 3rd party applications), or as a library integrated into an application. We recommend deploying the service on premise, on your own infrastructure. We also recommend that every Signing Agent instance be used to manage a unique agent ID, and that multiple instances be deployed (preferably on different cloud infrastructures) in order to meet a multiple signer threshold such as 2 or 3 out of 5. 


## Integrations

Supports|
-------- |
[Partner API](/developer-guides/partner-api/get-started)|
[Web3 Wallets](/developer-guides/api-services/web3-wallet)|


## Who uses Signing Agent?

Signing Agent is used by companies and developers requiring:

{% tabs %}
{% tab label="High volume; high-velocity" %}
Together, the transaction creation API, [Web3 API](/developer-guides/api-services/web3-wallet), and Signing Agent enable dApp developers, fund managers, and other high-volume, high-velocity transaction entities to programmatically create and approve transactions. All while maintaining custody over your digital assets.
{% /tab %}
{% tab label="dApps" %}
Signing Agent complements the full white-label product, Partner API, and the expanding microservices of Qredo API. Using either, you can support the automation of approvals through intuitive integration with your third-party App.
{% /tab %}
{% /tabs %}


## Version

Welcome to the Qredo Signing Agent v2. 

{% callout type="note" title="Signing Agent v1 is deprecated" %}
As of Q4 2022, Signing Agent v2 replaces v1: [Core Client](/developer-guides/signing-agent/v1/set-up-core-client) providing a much-simplified implementation.
{% /callout %}