---
comment:  Menu nesting required tbd Supports WebApp 50 shades // Supports Partner API AND Qredo API >> needs a page on policies
title: Approvals
author: Relocating some of TK> HB
proofedDate: 20221122
---

Within Qredo, the custody rules require an approval for:

- every transaction created
- every request to move assets **out** of a Qredo Wallet (withdrawl)

before it can be completed.

Approvers may be people that exist within the Qredo system who have set up a Personal account and agreed to take on a custody role, aka "trusted parties". These are Signing App approvers whose approvals are managed via the Mobile App. Alternatively, approvers may be [automated Signing Agents](/developer-guides/signing-agent), programmed to respond within specified constraints to auto-approve or reject transfer requests.

The collection of designated trusted parties forms your *trusted network*. Every Company (for Partner API) or Organization (for Qredo API) has one or more approvers that approve transactions across Funds and Wallets.

You may choose to let a Wallet [inherit its policy](#granular-control-over-approvals), or you can create granular control policies at a level that suits.


## Granular control over approvals

Not only does Qredo allow you to customize your approval groups and custody policies, the rulesets by which approvals may be made — such rulesets may be applied at various levels:

||[Partner API](/api-reference/partner-api)|[Qredo API](/api-reference/qredo-api)|
|------------------------|-----------------|-----------------|
|**Supported policy level**|Company|Organization|
||Fund|Fund|
||Wallet|Wallet|

Or the policy may be inherited from the top level down.

Similarly, for each entity you have the option to customize the trusted network for transactions and withdrawls.

{% callout type="note" title="Transactions and withdrawl policies can differ" %}
You may choose to have different a different policy controlling transactions and withdrawls. Learn how to [set up policies in the Web App](/user-guides/policies).
{% /callout %}

{% callout type="note" title="Understand how approvals control transactions" %}
See [transactions](transactions) to understand how approvals are part of the transaction flow.
{% /callout %}


