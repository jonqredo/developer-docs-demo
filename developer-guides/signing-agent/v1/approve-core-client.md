---
title: Approve Core Client
comment: Core client offering deprecated, need logs to monitor who is still using this service.
author: TK
proofedDate: noProof
hide:
  - navigation
---

This section explains how to perform Core Client custody using the `/coreclient` API endpoint:

* [fetch data for all requests](#get-all-pending-approval-requests) pending Core client custody: `GET /actions`
* [fetch details](#get-details-of-a-specific-action) of a specific action: `GET /action/{action_id}`
* [approve](#approve-action-pending-custody) a transaction pending Core client custody: `PUT /action/{action_id}`
* [reject](#reject-action-pending-custody) a transaction pending Core client custody: `DELETE /action/{action_id}`

The Core Client uses its own subset of the API. Each API call is performed on behalf of the Core Client, specified by its `client_id`. Every *transaction request* pending Core Client custody is referred to as *action* and is identified by its `action_id`.

In terms of the Core Client, all transactions pending approval are referred to as *actions*. Every action has a unique ID, as well as properties, such as *type* (transfer or withdrawal), *status* (pending, expired, approved, rejected), and timestamps of creation and expiry.

## Get all pending approval requests

This example fetches all transaction requests that require Core Client custody. In other words, these are all transaction requests in `pending` status. The response returns a JSON object with count of actions and relevant details.

### Example request: `GET /actions`

This example retrieves all actions in pending status: similarly to a Core Client feed. With this API resource, you pass your Core `client_id` as a URL parameter.

```bash
GET /coreclient/{client_id}/actions
```

### Example response: `GET /actions`

A successful response returns a list of available actions.

```json
{
  "count": 1,
  "actions": [
    {
      "id": "<action _id, such as: 21PWl0QKlKfOIkXbhdwxSinEsK2>",
      "type": "<tx type: 'ApproveTransfer' or 'ApproveWithdraw'>",
      "status": "<action status: 'pending', 'expired', 'approved', or 'rejected'>",
      "timestamp": "<Epoch timestamp when action was created>",
      "expireTime": "<Epoch timestamp when action expires if not approved/rejected>"
    }
  ]
}
```

## Get details of a specific action

Use `GET /action/{action_id}` to view various details of the transaction request, including origin and destination Wallet IDs.

{% callout type="note" title="View action details of any status, not just `pending`" %}
You can use this API call to view details of any action, regardless of its status, whether approved, rejected, expired or pending.
{% /callout %}

### Example request: `GET /action/{action_id}`

This example retrieves details of an action. With this API resource, you pass your Core `client_id` and `action_id` as URL parameters.

```bash
GET /coreclient/{client_id}/action/{action_id}
```

### Example responses: `GET /action/{action_id}`

The response is a little different when the transaction is of type `transferOut` or type `withdraw`: the final two parameters differ with each tx type.

```json
{
  "id": "<action ID, such as: 21PWl0QKlKfOIkXbhdwxSinEsK2>",
  "coreClientID": "<client ID, such as: 9niR8T...RW3xu>",
  "type": "<tx type: 'ApproveTransfer' or 'ApproveWithdraw'>",
  "status": "<current tx status: 'pending','approved', 'rejected', 'expired'>",
  "timestamp": "<Epoch timestamp when action was initiated, e.g. 1637843349>",
  "expireTime": "<Epoch timestamp when action expires if not approved/rejected>",
  "details": {
    "txType": "withdraw",
    "txID": "21PWl2yp1k4VocrXehPxgBjVCkH",
    "walletID": "<tx originating Qredo Wallet ID>",
    "asset": "asset type, e.g. BTC, ETH, BTC-TESTNET ETH-TESTNET",
    "amount": "<tx amount in asset base units>",
    "fees": "<tx or withdrawal fee>",
    "netAmount": "<tx or withdrawal asset amount in base units>",
    "reference": "<a reference value for this tx>",
    "benefitOf": "John Smith",
    "accountNo": "123-XX",
    "companyID": "<company ID, blank with withdrawals>",
    "fundID": "209fc...yvN1",
    "initiatedTime": "<Epoch timestamp when action was initiated, e.g. 1637843349>",
    "expires": "<Epoch timestamp when action expires if not approved/rejected>",
    "initiatedBy": "",
    "initiatorName": "",
    "initiatorType": "",
    "recipientAddress": "<L1 address when 'txType': 'withdraw'>",
    "recipientAddressName": "<name given to address when whitelisted>"
  }
}
```

{% comment %} 
???+ note "Response specifics: withdrawals vs transfers"
    
	The final 2 parameters in the response body differ with the tx type.

    **Transfers**
	
	* `recipientID` is listed (instead of `recipientAddress` that's with withdrawals). This is the recipient Qredo Wallet ID. 
	* `recipientName` is the parameter at the bottom and lists the name of recipient *company*: the one that holds the destination fund and Wallet.
    
	**Withdrawals**: 
	
	* `recipientAddress` is listed (instead of `recipientID` that's with transfers) and this is the recipient L1 wallet address.
	* `recipientAddressName` is the parameter at the bottom and lists the name you've given to the whitelisted address when creating it, as explained in Step 6. [Perform a withdrawal](/developer-guides/partner-api/walkthrough/06-perform-withdrawal#1-whitelist-a-withdrawal-address) of the Partner API quick start walkthrough.
{% /comment %} 

## Approve action pending custody

Use `PUT /action/{action_id}` to approve a transaction request pending Core Client custody. A successful response returns a `HTTP 200 OK` confirmation. You can only approve actions in `pending` status.

### Example request: `PUT /action/{action_id}`

This example approves a pending action. With this API resource, you pass your Core `client_id` and `action_id` as URL parameters.

```bash
PUT /coreclient/{client_id}/action/{action_id}
```

### Example response: `PUT /action/{action_id}`

The approve response returns `HTTP 200 OK`.

```json
{"code":200,"msg":"OK"}
```

## Reject action pending custody

Use  `DELETE /action/{action_id}` to reject a transaction request pending Core Client custody. A successful response returns a `HTTP 200 OK` confirmation. You can only reject actions in `pending` status.

### Example request: `DELETE /action/{action_id}`

This example rejects a pending action.  With this API resource, you pass your Core `client_id` and `action_id` as URL parameters.

```bash
DELETE /coreclient/{client_id}/action/{action_id}
```

### Example response: `DELETE /action/{action_id}`

The rejection response returns `HTTP 200 OK`.

```json
{"code":200,"msg":"OK"}
```
