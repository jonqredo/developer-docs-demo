---
comment:  Menu nesting required tbd todo does assigning permission work same way for SA? todo Does Administrator rule apply to Partner API (if so amend heading and wording)
title: Qredo API keys
author: Relocating some of TK > HB
proofedDate: 20221122
---


## TL;DR

- Administrators create and configure API keys

- Keys can provide access at different levels:
    - Account level (Personal or Organizational)
    - Wallet {% comment %} Vault {% /comment %}
- API keys make up part of your [custody layer](approvals)

{% comment %}
Your comment goes here
{% /comment %}

## Overview

Supported in|
-------- |
[Qredo API v1](/api-reference/qredo-api)|
[Partner API](/api-reference/partner-api)|

API keys may be provided to trusted parties to allow them to engage with your Organization, Funds, and Wallets. Individual API keys may be [configured](#api-key-control) to enable granular control over the access rights they confer.


## API key control

There are several layers of control afforded to API keys:

1. Permissions assigned to the key. See [generate keys](/developer-guides/qredo-api/generate-keys) for further information.

2. The Entity the key applies to. Assign permissions from the Organizational perspective or, if there are multiple Wallets, at the Wallet level:
 
![granular api key control](/images/glossary/granular-api-key.png)

## Administrators and the Qredo API

{% callout type="warning" title="Personal and Organization accounts are separated" %}
This means that you can't view the Organization's assets, Funds, and Wallets using a Personal account API key and secret pair; and vice-versa.
{% /callout %}

In your Organization, only accounts with the Administrator role have set up access to the Qredo API v1. This means that only an Administrator can:

* Create/delete the Organization API key & API secret pairs
* Set permissions of API keys
* Access API key and secret pairs to share with a trusted party

Since an Organization can have multiple administrators, each can generate an API key and secret pair for their individual use, or for use by trusted parties under their domain.

{% callout type="warning" title="Any Organization member with API key and secret can access the Qredo API v1" %}
Regardless of their role, any member of an Organization with an API key and API secret can access the Organizational data. It is up to an Organization Administrator to communicate a relevant key and secret pair to another member of the Organization. Use secure channels only!
{% /callout %}

