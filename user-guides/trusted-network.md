---
comment: 50s Web App flow // Preventing taking dev out of devDocs for UI // wip verify 3 ends the flow // working here policy update needs to be covered. Nikolas docs also say you can update a Fund's policy -- if so, this needs to be corrected 
title: Trusted Network
author: HB
proofedDate: noProof
---
Applies to|Supports|
-------- |-------- |
|Web App v1|[Qredo API](/developer-guides/qredo-api)|
||[Signing Agent](/developer-guides/signing-agent)|


This page guides you to edit your trusted network from the Web App. You may add:

- A Signing App user: a person who makes approvals via the Mobile App
- A [Signing Agent](/developer-guides/signing-agent): an automated approver


## Prerequisites

- You have an admin role over a Qredo account
- You are signed into the [Web App](https://qredo.network/signin)

## Add a member to your trusted network

1. Navigate **Account** > **Settings**

	> See the UI guide for [help switching accounts](switch-accounts).

1. Navigate to the tab **Members & Roles**, and click **Add Member** (bottom right).


{% tabs %}

{% tab label="For a Signing App user" %}
Enter the registered email address associated with the Qredo account. 

{% /tab %}

{% tab label="For a Signing Agent" %}
Enter the `agentID` or the Signing Agent's user-assigned name. 

{% /tab %}

{% /tabs %}


 ![Add Member2](/images/ui/50s_trusted-party.png)

1. Click **Review Organization**

## Policy members

Members of trusted networks must be added to [policies to be approvers](policies). Either you add trusted members when you create a policy or you can update an existing policy to include them.
