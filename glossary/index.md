---
comment: TBD
title: Qredo core concepts
pageTitle: Overview
author: 
--- 


You are in the right place to understand more about Qredo's products and services, use these pages to drill down and understand specific concepts.

{% comment %} 

We can consider using the following terms in the Glossay. (They were previously published in the Partner API section.)

???+ info "Qredo-related terminology"

    * **Qredo Network** (also interchangeable with Qredochain), the Qredo Network is a Layer 2 (L2) blockchain. Each transaction on Qredochain undergoes custody, which means that it is either approved or rejected in order to process. The native token on the Qredochain is the QRDO token [*kh-ree-doh*].
    * **Custody** - approval control over outgoing transactions of a Qredo Wallet. With Qredo, nearly all transactions out of any Qredo Wallet require an approval, regardless of asset type. Qredo accounts that perform custody using the Qredo Signing app are called approvers. Additionally, anyone with API access can set up a Signing Agent, which acts as a Partner API approver. 
    * **Qredo account** is an account that can access their personal Qredochain assets and Wallets. You cannot work with the API unless you have a Qredo account. To create an account, you must use the combination of the Qredo Web App (in your web browser) and Qredo Signing app (on your mobile device).
    * **Qredo Wallet** is the browser-based software that allows you to access and manage your assets on the Qredochain.
    * **Qredo Signing App** is the mobile application that allows you to approve or reject transactions. There is no other way for a person to approve transactions: they must use the Qredo Signing app, which is available on iPhone and Android.
    * **Asset** might have different meanings depending on context. When used in singular, *asset* is often interchangeable with *asset type* and refers to a Level 1 crypto asset (such as BTC or ETH) that is available on the Level 2 Qredo blockchain. When used in plural, *assets* are often referred to as *Wallet assets* or *transaction assets*, meaning the asset type and amount in question (e.g. 1 BTC in a Wallet or in a transaction).

## Partner API-specific concepts

For smooth Partner API operations experience, it is important that you are familiar with the list of concepts presented here (click to expand):

???+ info "Partner API-specific terminology"

    * `company` on the Qredo Network is an ID, part of a Qredo account, that holds funds and Wallets. You add approvers on a company level so each company has its own Trusted Network. Its members can be assigned to perform custody to Funds and Wallets that the company holds. The company is equivalent to an organization in the Qredo Web App but it is not the same thing.
    * `Fund` is the actual sub-entity to a company that holds Qredo *Wallets* and the approval rules for transactions with these Wallets: the so-called *custodial policies*. These policies also include the trusted parties that perform custody over transactions within that fund. 
    * `trusted_party` is a Qredo approver: either a Qredo user or a Signing Agent. Trusted parties belong to a company and form its trusted network. However, Funds and Wallets must additionally appoint approvers out of the trusted network and that is when they become eligible to apply custody.
    * **Custodial policies** define number of approvals needed for processing a transaction out of a given Qredo Wallet. There are two types of custodial policies: `policy_tx` for transfers (moving assets from one Qredo Wallet to another) and `policy_withdraw` for withdrawals (moving assets from a Qredo Wallet to a L1 blockchain Wallet). Custodial policies apply per fund (fund policies) or per Wallets held by a fund (custom policies). 
    * **Signing Agent** is an API-specific user type that performs custody: an API approver bot that can be further automated. It is implemented as a client-side software that connects to its dedicated Signing Agent service and has an approver identity which allows it to approve or reject transfer or withdrawal requests on the Qredochain. The Signing Agent instance is identified by its `client_ID` which correlates to a human approver ID. To help emulate the human approver experience, the Signing Agent connects to a dedicated WebSocket feed that prints out every new transaction that is pending approval. Additionally, the Signing Agent functionality can be extended beyond a "regular" API approver and can act on behalf of an *exchange*. This allows the exchange Signing Agent to perform additional tasks, such as approve requests to connect Qredo Wallets, perform sweep transactions and act as a "master" approver for dedicated Wallets.
{% /comment %} 
