---
comment: Menu nesting required tbd // to do split into UI guide and concept guide // BUCKET used for breaking out concepts that will span the microservices but don't belong in the existing pages. / wip why does Organization account not let you turn on Test Net == diff flow to Personal acct, being fixed? 
title: Overview of Qredo accounts
author: Relocating some of TK> HB
proofedDate: 20221122
---


## Account types

Applies to|
-------- |
|Web App v1|

### Personal account

Every person must have a Personal account linked via the Qredo Mobile App.

### Organization account

An Organization account is a special type of account linked to a Personal Qredo account. Each Personal account may be linked to one or more Organization accounts, and each Organization can have multiple Funds and Wallets. 

Such Funds and Wallets are separate from those that belong to your Personal account. 

The purpose of the Organization is to enable multiple Qredo Personal accounts to participate in shared management and custody of Wallets and assets that belong to the Organization. 

To achieve this governance layer, Organizations may be linked to people's Personal accounts *and* to automated Signing Agents. This means that several individuals, or Signing Agents, may be assigned roles within one Organization to participate in managing Organization Wallets and assets. 

Qredo accounts added to the Organization are called *members*, and each of them is assigned at least one of the [available roles](#organization-members-and-roles).



## Navigate accounts

### Create Organization accounts

[Create and manage your Organizations](https://qredo.zendesk.com/hc/en-us/articles/4404077348753-Creating-an-Organization) via the Qredo Wallet Web App. The Web App allows you to switch between your Personal and Organization accounts. 

{% callout type="note" title="Coming soon" %}
Providing account setup from API is on our roadmap. 
{% /callout %}

### Switch between Personal and Organization accounts

When you log into the Qredo Wallet Web App, you default to your Personal account. You must be in your Organization account in order to perform your duties within the Organization.

See the [UI guide](/user-guides/switch-accounts) for assistance on switching accounts.

## Manage accounts

### Organization members and roles

An Organization can have multiple members, and each of these members is assigned a *role*. The available roles are:

Role | Level of access
-------- | -----------
Administrator | Has full control over the Organization: can create and access all Funds and Wallets, add or remove members, submit transactions, and approve/reject transactions. In other words: the Administrator can do everything that all other roles can.  **Only an Administrator in an Organization can create Organization API key and secret pairs.**
Fund Organizer | Has control over Funds and Wallets they are assigned to. The Fund Organizer can create Wallets in a Fund they are part of. 
Approver | Has approval control over transfers/atomic swaps/withdrawals from Wallets they are assigned to. Typically, approvers perform their duties using the Qredo Signing app.
Trader | Can initiate transfers/atomic swaps/withdrawals out of any Wallet when working within that Wallet's custody ruleset.

{% comment %} Can the Trader initiate a transferOut of any Wallet within the Org? {% /comment %}

Within the Organization, the Administrator can perform any action available to the other three roles. Also, the Administrator is the only role with API access. This means that all other roles use the Qredo Wallet Web App/Signing App to perform their duties within the Organization, unless the key and secret are shared by an Administrator.

Learn more about [Organization roles](https://qredo.zendesk.com/hc/en-us/articles/4405882947089-Organization-Members) that each member can have. Note. the creator of a Personal account is always assigned an administrator's role. 

### Add Organization members

You can add members during the process of [creating an Organization](https://qredo.zendesk.com/hc/en-us/articles/4404077348753-Creating-an-Organization). The flow with adding members at a later stage is similar.

{% callout type="note" title="You can only add existing Qredo accounts" %}
The Qredo accounts you invite to participate as members of your Organization must already exist as Qredo accounts. Remember that you can't (and must not attempt to) create accounts on behalf of others. 
{% /callout %}

Go to the "Organization **Settings**" page and perform the following steps:

1. Select the **Members & Roles** tab.
2. In the bottom right corner, click **Add Member** to open the **Edit Organization** box that allows you to proceed.
3. In the **Add member** text box, type in the email address of the Qredo account you want to add as a member.
4. Click **Search**. The selected account email and username should populate and replace the text box. If account details do not populate, make sure to type in the email address correctly and repeat the step.
5. In the newly displayed account details box, select the role you want to grant to the selected member. You can assign multiple role access to your member. For example, you can make them a Fund Organizer and an Approver, if this makes sense for your purposes. If you select an *Administrator* role, the remaining roles are selected by default.
6. **Important!** Click the **+** button, located adjacent to the **Select Role** drop-down list. You will fail to add your new member if you omit this step.
7. The box updates with the new entry under New Members. Click **Review Organization** to view your changes.
8. The **Edit Organization** box updates with your newly added member details. Click **Save Changes** to finalize the process.

That's it! Your newly added member will receive a notification on their mobile device. The new member must access the Qredo Signing App on their mobile and accept the request. Until then, the Administrator can view them as added to the Fund, but under Status: **Awaiting Approval**.

Once approved, their status under **Members & Roles** tab changes to **Approved**.



