---
comment: With no help center for Web App or signing app, need common material for many services
title: Generate API keys
author: HB
proofedDate: 20221217
---

Applies to|Supports|
-------- |-------- |
|Web App v1|[Qredo API](/developer-guides/qredo-api)|
||[Signing Agent](/developer-guides/signing-agent)|


This page guides you to select the desired account, generate API keys, and assign permissions to those keys.

## Prerequisites

- You have an admin role over a Qredo account
- You are signed into the [Web App](https://qredo.network/signin)

## Select the account

1. Navigate **Account** > **Settings** to select the required account: Personal or Organization

    > See the UI guide for [help switching accounts](switch-accounts).

## Generate and copy the key

1. Under Settings, select the **API** tab.

    ![api tab](/images/admin/api-tab.png)


1. In the bottom right corner, click **Create API Key** to open a box that requires you to enter a new **API name**.

    ![create key](/images/admin/create-api-key.png)

1. Enter a functional name for the API key and click **Create**. Copy the *API key* and *API secret* displayed. 

{% callout type="warning" %}
Ensure secure storage of your *API secret*, as you will not be able to visualize it again. There is no way to recover a lost secret. If a key is lost, repeat steps 1&ndash;3 to generate a new *API key and API secret* pair.
{% /callout %}

## Assign permissions to an API key

1. From the hamburger menu, select Permissions.

    ![assign permissions](/images/admin/key-permission.png)

1. Assign the permissions you require for that key and save.

    ![slider permissions](/images/admin/slider-permissions.png)


{% callout type="warning" %}
To give you granular control over which services you associate with each API key, you must opt in from those available. [Learn more](/glossary/api-keys) in the Knowledge Base.
{% /callout %}



