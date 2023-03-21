---
comment: Menu nesting required tbd  Zendesk not supporting high-level linking, temp page to support Web3 API launch // todo Add to table -- is MPC network involved in signing (ticket== DOC-26)?  todo Add Qredo Station to table (ticket== DOC-25). // 
title: Introduction to Wallets
author: Zendesk(NT) & HB
proofedDate: 20021122
---

A [Qredo Wallet](https://qredo.zendesk.com/hc/en-us/articles/4407297058321-Qredo-Wallet) holds and/or provides custody over your digital assets. Each digital asset type must have its own Wallet.

A user may create an infinite number of Wallets that are grouped in collections called Funds.

## Where are my assets?

Where your assets are held depends upon the Wallet class you choose. 


Wallet class| Asset location/s | Exchanges supported |Further reading
-------- | ----------- | ----------| ----------
Connected | External L1 | Y | [Help center](https://qredo.zendesk.com/hc/en-us/articles/4406882877457-Connected-Wallet){% target="_blank" %}   
Standard | Qredo L2 and external chains |N | [Help center](https://qredo.zendesk.com/hc/en-us/articles/4405882425105-Standard-Wallet) 
Vesting | Qredo L2 | N | [Help center](https://qredo.zendesk.com/hc/en-us/articles/4405882480913-Vesting-Wallet)

{% comment %} todo: add Qredo Station to this {% /comment %}

## Connected Wallets

Unlike a Standard Wallet, Connected Wallets do not hold assets. The assets remain on the EVM chain. Rather, they provide Qredo's secure custody layer over transactions performed by external Wallets. The Connected Wallet class includes several Wallet types:

- Web3 API Wallet

    Compatible with [Qredo API v1](/api-reference/qredo-api)

- MMI Wallet

    External L1 [MetaMask Institutional integration](https://qredo.zendesk.com/hc/en-us/articles/5167336022417-Qredo-MetaMask-Institutional-Partnership): available from Web App

{% comment %} 
check with John MW, I think these 2 can go

- Multi-Counterparty Wallet

    Compatible with [Partner API](/api-reference/partner-api)

- Dedicated Counterparty Wallet

    Compatible with [Partner API](/api-reference/partner-api) 
{% /comment %}

- WalletConnect

    External [EVM-compatible L1 Wallet](https://qredo.zendesk.com/hc/en-us/articles/7782945040529-Qredo-WalletConnect-Integration) available from the Web App


{% callout type="note" %}
	All Connected Wallets support the allowlisting of addresses to control where assets may be sent. Learn more in the [help center](https://qredo.zendesk.com/hc/en-us/articles/10626460462097-Whitelisting-Addresses-Beta-).
{% /callout %}


## Which Wallets work with Qredo API?

To integrate with Qredo directly through Qredo API v1 services, you will use one of the supported Wallet types:

### Web3 API Wallets

You can use Web3 API Wallets to automate transaction submission, signing, and approval via the [Web3 API Wallet service](/developer-guides/api-services/web3-wallet).

The Web3 API Wallet leverages the full Qredo custody suite, giving you, your trusted parties, and/or the Signing Agent, control over transactions. Furthermore, because API key permissions can be limited to one or more Web3 API Wallets, you gain additional configuration flexibility.

When used alongside a Signing Agent for automating the approval process, Web3 API Wallets provide a programmatic custody layer to existing transaction flows that occur in external (non-Qredo) Wallets.

{% callout type="note" %}
Connected Wallets rely on Qredo fee station being in credit (required to pay [Qredo fees](https://qredo.zendesk.com/hc/en-us/articles/10869841370129-Qredo-Station-FAQ)).
{% /callout %}


### Read-only API

The Read-only API lets holders of Qredo Wallets view basic information about their account, Wallet, and Funds. It supports the following Wallet classes:

- Connected: Web3 API Wallets
- [Standard Wallet](https://qredo.zendesk.com/hc/en-us/articles/4405882425105-Standard-Wallet)




