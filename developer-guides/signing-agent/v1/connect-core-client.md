---
title: Connect Core Client
comment: Core client offering deprecated, need logs to monitor who is still using this service.
author: TK
proofedDate: noProof
hide:
  - navigation
---

After you have created a Core Client instance, you can connect it to a WebSocket feed to listen for transactions pending custody. You will be using the Core Client websocket `id`as [created in the previous section](set-up-core-client) to connect to the unique WebSocket `feed`.

This topic presents the process to connect a Core Client instance to the WebSocket service. For illustrative purposes, we will use the familiar Signing Client tool, but this time take advantage of its Core Client functionality.

You can create and connect as many Core Client instances as you need.

## Connect to feed

### Golang example
The example uses the example Signing Client provided by Qredo.

**Prerequisite:** You must have Golang installed.

1. Clone the repo at [https://github.com/qredo/partner-api-sign](https://github.com/qredo/partner-api-sign).

2. `cd` to repo:

```bash
cd partnerapi-sign
```
3. Build the app in CLI

{% tabs %}

{% tab label="Linux/Mac" %}
You can also run this command on Windows using PowerShell:
```go
go build -o partnerapi-sign
```
{% /tab %}

{% tab label="Windows" %}
Use command prompt: `cmd.exe`:
```go
go build -o partnerapi-sign.exe
```
{% /tab %}
{% /tabs %}


4. Start listening to the WebSocked feed:

{% tabs %}

{% tab label="Linux/Mac" %}
You can also use this command on Windows using PowerShell:

```
./partnerapi-sign websocket -url wss://api.qredo.network/api/v1/p/coreclient/{client_id}/feed
```
{% /tab %}

{% tab label="Windows" %}
Use command prompt: `cmd.exe`:
```bash
partnerapi-sign.exe websocket -url wss://api.qredo.network/api/v1/p/coreclient/{client_id}/feed
```
{% /tab %}
{% /tabs %}

### Python example

**Prerequisites:** You must have Python v3.6.1 (or later) with the `websocket` library installed: (use `pip3 install websocket` in CLI to install it).

Once you have the prerequisites, run the following example script:

```python
from websocket import create_connection
ws = create_connection("wss://api.qredo.network/api/v1/p/coreclient/{client_id}/feed", header={"x-api-key": "<`API_key`>","x-sign": "<SIGNATURE>", "x-timestamp": "1111111111"})
while True:
 result =  ws.recv()
 print (result)
```
---

Upon success, the CLI displays `connected` actions from the Core Client in compressed JSON format.

```
C:\path-to-core-client>partnerapi-sign.exe websocket -url wss://api.qredo.network/api/v1/p/coreclient/9niR8...RW3xu/feed
url:  wss://api.qredo.network/api/v1/p/coreclient/9niR8...RW3xu/feed
Hash: ef547...2a265
x-sign: VoYl4fM...nunLgA
x-timestamp: 1636125880
connecting to wss://api.qredo.network/api/v1/p/coreclient/9niR8...RW3xu/feed
connected
```

{% callout type="note" title="Your Core Client is connected and your feed is live" %}
From the moment your Core Client connects, any transaction or withdrawal request (from a Wallet that uses this Core Client as a custodian) will be displayed in the CLI.

See the [websocket feed examples](#receive-live-feed) below.
{% /callout %}

Now that you are connected to your feed, you start to receive transactions pending approval.

## Receive live feed

When you have set up and connected your Core Client as explained above, you will be getting feed with each request for transactions out of Qredo Wallets that this Core Client applies custody to.

{% callout type="note" title="Core client gets feed for specified Wallets only" %}
Do not forget that the Core Client must be an appointed approver for transactions out a Wallet and all transaction/withdrawal requests pertain to such Wallets only.
{% /callout %}

Note that if your Core Client disconnects from the WebSocket feed, you must reconnect it manually again in order to start receiving your feed again. Once you reconnect, the Core Client will print out all current requests pending approval, including ones that have been submitted while the Core Client was disconnected.

{% callout type="note" title="Query for queued up requests" %}
When the Core Client is disconnected, it obviously cannot receive requests. Regardless of the live feed, you can check out if there are transaction/withdrawal requests queued up using the `GET coreclient/{client_id}/actions` resource as explained in the next section: [Core client custody](approve-core-client#get-all-pending-approval-requests).
{% /callout %}


### Transaction feed

When a transaction request out your Wallet is submitted, the Core Client CLI window prints out transaction requests formatted as follows:

``` json
{
  "coreClientID": "<Core Client ID, e.g.: '9niR8...RW3xu'>",
  "expireTime": "<timestamp in Epoch when request expires, e.g. 1637855804>",
  "id": "<action ID, e.g.: 21Mg07BDBE09AARwUPJcVQT0fnO>",
  "status": "<tx status: 'pending'>",
  "timestamp": "<timestamp in Epoch when request was submitted, e.g. 1637756144>",
  "type": "<type of transaction to approve: 'ApproveTransfer' for transfer>"
}
```

{% callout type="note" title="The action `id` is not the same as `tx_id`" %}
Do not mistake the `tx_id` you receive with each transaction request for the `id` received on the Core Client side. This difference is obvious in the JSON response of the `GET /transfer/{tx_id}` request where the `actionID` is listed as part of the tx object.
{% /callout %}

### Withdrawal feed

If you perform tests with withdrawals, do not forget to have the destination withdrawal wallet *whitelisted*, as explained in the Partner API walkthrough [Step 6: Perform a withdrawal](/developer-guides/partner-api/walkthrough/06-perform-withdrawal#1-whitelist-a-withdrawal-address).


When a withdrawal request out your Wallet is submitted, the Core Client CLI window prints it out in format similar to that of a transaction request:

``` json
{
  "coreClientID": "<Core Client ID, e.g.: '9niR8...RW3xu'>",
  "expireTime": "<timestamp in Epoch when request expires, e.g. 1699998800>",
  "id": "<action ID, e.g.: 21PWl0QKlKfOIkXbhdwxSinEsK2>",
  "status": "<tx status: 'pending'>",
  "timestamp": "<timestamp in Epoch when request was submitted, e.g. 1637843349>",
  "type": "<type of transaction to approve: 'ApproveWithdraw' for withdrawal"
}
```

{% callout type="note" title="Transfer vs Withdrawal in feed" %}
Note that the format with both request types is the same with the exception that transfers appear of type `ApproveTransfer` while withdrawals are of type `ApproveWithdraw`.
{% /callout %}

### Atomic swaps feed

The atomic swap is a transaction type that two counterparties swap assets, e.g. some BTC in exchange for some ETH. This means both counterparties have a swap transfer out transaction. These are covered in detail in the [Atomic swaps](/developer-guides/partner-api/atomic-swaps) section.

## Next

Next, [approve the core client](approve-core-client).