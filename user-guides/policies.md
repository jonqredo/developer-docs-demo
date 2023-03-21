---
comment: 50s Web App flow // Preventing taking dev out of devDocs for UI // working here
title: Policies
author: HB
proofedDate: noProof
---

Applies to|Supports|
-------- |-------- |
|Web App v1|[Qredo API](/developer-guides/qredo-api)|
||[Signing Agent](/developer-guides/signing-agent)|


This page guides you to create and edit your policies from the Web App. 

> Members of trusted networks must be added to policies to be approvers. Either you add trusted members when you create a policy or you can update an existing policy to include or remove them.

{% callout type="note" title="Custom policies control who may approve:" %}
		- Transactions (asset transfers and swaps)

		- Withdrawls

		Each may have their own policy group
{% /callout %}

	Learn more about policies in our [Knowledge Base](/glossary/approvals) and our [help center](https://qredo.zendesk.com/hc/en-us/articles/4628260190737-Setting-up-Fund-wallet-approval-policy).


## Create a new Policy

### Prerequisites

- You have an admin role over a Qredo account
- You have added any required members to your [trusted network](trusted-network)
- You are signed into the [Web App](https://qredo.network/signin)

Note that, if you attempt to create a Fund or Wallet before setting up your transaction approval policy, you will receive this warning:

![create approval policy](/images/glossary/default-approval-policy.png)

A similar warning will be shown if you create a Fund with no withdrawal approval policy.

{% callout type="note" title="Learn more:" %}
	- [Overview of Qredo custody and Wallets](https://qredo.zendesk.com/hc/en-us/articles/4407297058321-Qredo-Wallet)

	- [Set up a Fund and Wallet](https://qredo.zendesk.com/hc/en-us/articles/4404077381265-Creating-a-Fund-Setting-Up-Your-Wallet) 

	- [Set up an approval policy](https://qredo.zendesk.com/hc/en-us/articles/4628260190737-Setting-up-Fund-wallet-approval-policy) 
{% /callout %}

### Option 1: Create a Fund

1. From the main page, select **Create Fund** and follow the Wizard. Step 2 ask you to create the:

	- Member group for the Fund
	- Transaction approval policy for the Fund
	- Withdrawl approval policy for the Fund

### Option 2: Create a Wallet

1. From the main page, select the Fund you wish to add a Wallet to.

1. From the Fund page, select **Add Wallet** and follow the Wizard. Step 2 ask you to create the approval policy/s for the Wallet. Depending on the Wallet type, you may:

	- Inherit from the withdrawl policy of the Fund
	- Inherit from the transaction policy of the Fund
	- Set a custom policy for the Wallet

## Update an existing policy

### Prerequisites

- You have an admin role over a Qredo account
- You have added any required members to your [trusted network](trusted-network)
- You have an existing policy in place to edit
- You are signed into the [Web App](https://qredo.network/signin)


### Step 1: Access the Policy control page


1. Navigate **Account** > **Settings**

	> See the UI guide for [help switching accounts](switch-accounts).

1. Select the Fund or Wallet you wish to manage and navigate to the **Policies** tab.

	![control policy](/images/ui/edit-policy-area.png)

1. Select which policy you wish to update:

	- Transaction
	- Withdrawl
	- Connected


{% callout type="note" title="Connected Wallet policies" %}
A Connected Wallet may inherit the transaction and withdrawl policies of its Fund, or have specific policies applied.
{% /callout %}

![control policy](/images/ui/50s_connected-wallet_policy.png)

1. Click **Edit Fund Policy**



### Option 1: Add a trusted network member to your policy
	

1. Search for the member of the trusted network you wish to add to a policy.

	![control policy](/images/ui/50s_edit_policy.png)

1. Click **Review Changes**.

### Option 2: Delete a trusted network member from your policy
	

1. From the list of members, idenfity which you need to delete from the policy.

	![control policy](/images/ui/50s_delete_member_from_policy.png)

1. Click **Review Changes**.


