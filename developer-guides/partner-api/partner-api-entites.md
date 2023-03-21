---
comment: WiP, requires updates regarding core client>signing agent (partial pass not fully done), exchanges // used as a bin for data that needs tidying up >> Move this data to Knowledge base and deprecate. JH - Not updated to MarkDocs till confirmed if this is staying here or moving under concepts
author: TK
title: Partner API entities
---

This section presents various entities used within the Partner API.

## Main API entities

### Assets

**Assets** can be queried without stating a **Company_ID**.
It is a useful endpoint to query prior to performing a transaction.

`GET https://api.qredo.network/api/v1/p/assets`

* EXAMPLE UNCOMPRESSED ASSETS REQUEST

```curl
curl -X GET "https://api.qredo.network/api/v1/p/assets" 
-H "accept: application/json" \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
```

* EXAMPLE ASSETS RESPONSE

```json
{"assets":
[
  {"code":"BTC",
  "name":"Bitcoin",
  "unitCode":"BTC",
  "scale":100000000,
  "enabled":true,
  "testAsset":false
  },
  {"code":"BTC-TESTNET",
  "name":"Bitcoin Testnet",
  "unitCode":"BTC-TEST",
  "scale":100000000,
  "enabled":true,
  "testAsset":true
  },
  {"code":"ETH",
  "name":"Ethereum",
  "unitCode":"ETH",
  "scale":1000000000,
  "enabled":true,
  "testAsset":false
  },
  {"code":"ETH-TESTNET",
  "name":"Ethereum Testnet",
  "unitCode":"ETH-TEST",
  "scale":1000000000,
  "enabled":true,
  "testAsset":true
  }
]
}
```

#### Asset Object

Attribute | Description
---------- | -------
code | String - Asset Code e.g. BTC-TESTNET
name | String - Asset Name e.g. Bitcoin Testnet
unitCode | String - Asset unit code e.g. BTC-TEST
scale | Number - The scale expressing the asset units e.g. 100000000
enabled | Boolean - TRUE or FALSE Whether the asset is supported by Qredo or not.
testAsset| Boolean - TRUE or FALSE - Whether the asset is a test asset or not.

### Fees

**Fees** can be queried without stating a **Company_ID**.
It is a useful endpoint to query prior to performing a transaction. You can calculate the real-time fees for specific assets and transaction types.

`POST https://api.qredo.network/api/v1/p/fees`

{% callout type="note" title="Fees at a glance" %}

* 0.1 bps (or 0.001%) for transfers/atomic swaps
* 0.1 bps (or 0.001%) for withdraws
* NO charge on deposits.

{% /callout %}

#### Create Fee Request
* EXAMPLE UNCOMPRESSED FEE REQUEST

```curl
curl -X POST "https://api.qredo.network/api/v1/p/fees" 
-H "accept: application/json" \
-H  "Content-Type: application/json"  \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
-d "{
  "type":"transferOut",
  "asset":"BTC-TESTNET",
  "amount":200000
  }"
```

* EXAMPLE COMPRESSED FEE REQUEST

```curl
curl -X POST "https://api.qredo.network/api/v1/p/fees" -H  "accept: application/json" -H  "X-API-KEY: <YOUR_API_KEY" -H  "Content-Type: application/json" -d "{\"type\":\"transferOut\",\"asset\":\"BTC-TESTNET\",\"amount\":200000}"
```

* EXAMPLE FEE RESPONSE

```json
{"netAmount":200000,"amountToSend":200200,"fees":200}
```

Provide details of your transaction to obtain real-time fees.


Parameters |  Description
--------- | -----------
type | Transaction Type e.g. transferOut See [Transaction Types](#transaction-types)
asset | Asset Type e.g. BTC-TESTNET
amount | The asset amount for the transaction e.g. 200000


### Company

A Company represents a customer account created by a partner. Multiple companies can be created corresponding to multiple customer accounts. A company must exist before any transaction can take place. 

#### Company Object 

Attribute | Description
---------- | -------
name* | String - This is a required field. The name allocated to the Company. e.g. Big Funds Inc.
city | String - The town or city the company is located. e.g. Paris
country| String - This is the ISO alpha-2 country code e.g. FR
domain | String - This is the website domain for the company e.g. bigfund.com
ref| String - This is your reference for the company.
company_id| String - This is the unique Company ID. 

#### Create Company

* EXAMPLE UNCOMPRESSED REQUEST

```curl
curl -X POST "https://api.qredo.network/api/v1/p/company" 
-H  "accept: application/json" 
-H  "X-API-KEY: <YOUR_API_KEY" 
-H  "Content-Type: application/json" 
-d "{
  "name":"Big Funds Inc.",
  "city": "Paris",
  "country":"FR",
  "domain":"bigfund.com",
  "ref":"<YOUR_REFERENCE>"
  }"
```
* EXAMPLE COMPRESSED REQUEST

```curl
curl -X POST "https://api.qredo.network/api/v1/p/company" -H  "accept: application/json" -H  "X-API-KEY: <YOUR_API_KEY" -H  "Content-Type: application/json" -d "{\"name\":\"Big Funds Inc.\",\"city\":\"Paris\",\"country\":\"FR\",\"domain\":\"bigfund.com\",\"ref\":\"YOUR_REFERENCE\"}"
```

This is the first step. To create a company, the minimum required parameter is **name**.

`POST https://api.qredo.network/api/v1/p/company`

Parameters | Description
-------|--------
name | Required. String - The name of the Company
ref | String - This is your reference for the company.
city | String - The town or city the company is located. e.g. Paris
country | String - This is the ISO alpha-2 country code e.g. FR. See [List of Country Codes](https://www.iso.org/obp/ui/#search).

Returns a Company Object with a unique Company ID.

#### Retrieve Company

* EXAMPLE UNCOMPRESSED REQUEST

```curl
curl -X GET "https://api.qredo.network/api/v1/p/company/{COMPANYID}" 
-H "accept: application/json" \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
```

* EXAMPLE COMPRESSED REQUEST

```
curl -X GET "https://api.qredo.network/api/v1/p/company/<COMPANY_ID>" -H  "accept: application/json" -H  "X-API-KEY: <YOUR_API_KEY>"
```

Retrieve details about a Company from a Company_ID. Includes the information used to create the Company.

`GET https://api.qredo.network/api/v1/p/company/{COMPANY_ID}`

Parameters | Description
-------|--------
company_id| Required. String - The unique company identifier.

Returns the Company Object associated with the Company_ID.


#### Update Company

Updates the specified Company by setting the values of the parameters passed. Any parameters not provided will be left unchanged.

`PUT https://api.qredo.network/api/v1/p/company/{COMPANY_ID}`

All Parameters are optional except for Company_ID. 

Parameters| Description
-----|-----
company_id| Required. String - This is the unique Company ID.  
name| String - Company name
city | String - Company town/city 
country | String - This is the ISO alpha-2 country code
domain | String - This is the website domain for the company e.g. bigfund.com
ref| String - This is your reference for the company.

Returns the **Company_ID** and **Ref**.

* EXAMPLE UNCOMPRESSED REQUEST

```curl
curl -X PUT "https://api.qredo.network/api/v1/p/company/{COMPANY_ID}" 
-H "accept: application/json" \
-H  "Content-Type: application/json"  \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
-d "{
  "name":"Big Funds",
  "city":"London",
  "country":"UK",
  "domain":"bigfunds.co.uk",
  "ref":"<YOUR_REFERENCE>"
  }"
```

* EXAMPLE COMPRESSED REQUEST

```curl
curl -X PUT "https://api.qredo.network/api/v1/p/company/{COMPANY_ID}" -H  "accept: application/json" -H  "X-API-KEY: <YOUR_API_KEU>" -H  "Content-Type: application/json" -d "{\"name\":\"Big Funds\",\"city\":\"London\",\"country\":\"UK\",\"domain\":\"bigfunds.co.uk\",\"ref\":\"<YOUR_REFERENCE>\"}"
```

### Search Company

`GET https://api.qredo.network/api/v1/p/company/search`

Search for a company by entering 3 letters in the query field. 


Parameters |  Description
--------- | -----------
query | String 3 letters

Returns the total_count for the number of matches along with details of each matching company. 


* EXAMPLE UNCOMPRESSED REQUEST

```curl
curl -X GET "https://api.qredo.network/api/v1/p/company/search?query=big" 
-H "accept: application/json" \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
```

* EXAMPLE COMPRESSED REQUEST

```curl
curl -X GET "https://api.qredo.network/api/v1/p/company/search?query=big" -H  "accept: application/json" -H  "X-API-KEY: <YOUR_API_KEY>"
```

* EXAMPLE RESPONSE

```json
{
  "total_count":4,
  "matches":[
    {
      "company_id":"<COMPANY_ID_RESULT_1>",
      "name":"Big Org",
      "domain":""
    },
    {
      "company_id":"COMPANY_ID_RESULT_2",
      "name":"Big Funds Inc.",
      "domain":"bigfund.com"
    },
    {
      "company_id":"COMPANY_ID_RESULT_3",
      "name":"Big Fund",
      "domain":""
    },
    {
      "company_id":"COMPANY_ID_RESULT_4",
      "name":"big Fund",
      "domain":""}
      ]
      }
```

## Trusted Network

A Trusted Network is the collective term for all Trusted Parties - Users, Counterparties and Custodians. 

A Trusted Network is associated with a specific Company by its Company_ID. 

It is within this network that you can perform transactions and withdrawals whilst ensuring the implementation of secure, decentralized custody. 

At least one **User** must be added as a **Trusted Party** to create a Trusted Network. The minimum required fields are **address** and **type**. {% comment %} what `type`? Not required - confirm{% /comment %}

### Add User

A User is an individual who is added to the Qredo Network. Once added, funds can be transferred to them using the API. A user must be added to the Trusted Network as a Trusted Party to act as a custodian.

To add a User to the Qredo Network, they must join by registering and creating an account via the [Qredo website](https://www.qredo.com/) and selecting 'Join the Network'.

### Add Trusted Party

A Trusted Party can be a Counterparty for a transaction and/or a Custodian. Use this call to make a request to add a user or entity as a Trusted Party. The user or entity is only added as a Trusted Party after they have approved the request.

{% callout type="note" title="Before you start" %}

A User must already be part of the Qredo Network to be added as a Trusted Party. To join the Qredo Network all users must register and create an account via the [Qredo Website](https://www.qredo.com/) and selecting 'Join the Network'.

{% /callout %}

`POST https://api.qredo.network/api/v1/p/company/{company_id}/trustedparty`


Parameters | Description
---------- | ---------
company_id| Required. String - This is the company_id returned when you created a Company.
address| Required. String - This is the User's email address.
type| Required. String - Specified as 'user'
name| Optional. String - Name of Trusted Party

Returns a standard `HTTP 200 OK` Response to indicate the request has been made to add a **Trusted Party**. The request needs to be approved by the intended Trusted Party via the **Qredo Signing app** on their mobile device or via the the **Signing Agent (fka Core Client)**.

* EXAMPLE ADD TRUSTED PARTY REQUEST

```curl
curl -X POST "https://api.qredo.network/api/v1/p/company/{company_id}/trustedparty" 
-H "accept: application/json" \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \ 
  -d "{
    "address": "rosa@parks",
    "type": "user"
    }
```
* EXAMPLE ADD TRUSTED PARTY RESPONSE

```json
{"code":200,"msg":"OK"}
```
### Retrieve a list of Trusted Parties

Retrieves all Trusted Parties associated with a Company. This endpoint can be used to confirm that a user or entity has accepted a request to be a Trusted Party. Upon successful addition of a Trusted Party a 'Trusted_Entity_ID' is returned. 

`GET https://api.qredo.network/api/v1/p/company/{company_id}/trustedparty`

* EXAMPLE TRUSTED PARTY LIST RESPONSE

```json
  "company_id": "<COMPANY_ID>",
  "total_count": 0,
  "list": [
    {
      "company_id": "<COMPANY_ID>",
      "total_count": 1,
      "list": {
        "trusted_entity_id": "<TRUSTED_ENTITY_ID>",
        "name": "Rosa Parks",
        "type": "user",
        "address": "rosa@parks",
        "internal": true,
        "created": "<TIMESTAMP>"
      }
    }
  ]
}
```

### Delete a Trusted Party

Remove a Trusted Party from a company Trusted Network.

`DELETE https://api.qredo.network/api/v1/p/company/{company_id}/trustedparty/{trustedparty_id}`

* EXAMPLE REMOVE TRUSTED PARTY

```
curl -X DELETE "https://api.qredo.network/api/v1/p/company/<COMPANY_ID>/trustedparty/<TRUSTED_ENTITY_ID>" 
-H "accept: application/json" \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
```

Parameters | Description
---------- | ---------
company_id| Required. String - This is the company_id returned when you created a Company.
trusted_entity_id| Required. String - This is returned by retrieving all Trusted Parties (above)

Returns standard `HTTP 200 OK Response` to indicate a **Trusted Party** has been removed successfully.

## Core Client

A Signing Agent (fka Core Client) acts as an automated Custodian for transactions. A Signing Agent (fka Core Client) allows you to set up a WebSocket feed and listen to actions.

### Core Client Object

Attributes | Description
-----|------
id | Unique ID for the Signing Agent (fka Core Client)
name | Signing Agent (fka Core Client) name
feed | Unique Websocket Feed
timestamp | Time in epoch

### Create a Core Client

Create a Signing Agent (fka Core Client) to programmatically automate custodian flows. 

`POST https://api.qredo.network/api/v1/p/coreclient`



Parameters | Description
---------- | ---------
name | Required. String

Returns a Core Client details object. 

* EXAMPLE CREATE A Signing Agent (fka Core Client)

```curl
curl -X POST "https://api.qredo.network/api/v1/p/coreclient" 
-H "accept: application/json" \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
-d "{
  "name":"meaningful_name"
  }"
```

### Retrieve a list of all Core Clients

View all registered Signing Agent (fka Core Client)s

`GET https://api.qredo.network/api/v1/p/coreclient`


Parameters | Description
---------- | ---------
company_id |Required. String - This is the company_id returned when you created a Company.

Returns a list of all Signing Agent (fka Core Client)s for a Company ID is returned.  

* EXAMPLE GET CORE CLIENTS RESPONSE

```json
{"count":3,
"clients":[
  {
   "id":"<CORE_CLIENT_ID_1>",
   "name":"alpha",
   "feed":"wss://api.qredo.network/api/v1/p/coreclient/<CORE_CLIENT_ID_1>/feed",
   "timestamp":1607437910},
  {
   "id":"<CORE_CLIENT_ID_2>",
   "name":"beta",
   "feed":"wss://api.qredo.network/api/v1/p/coreclient/<CORE_CLIENT_ID_2/feed",
   "timestamp":1607444404},
  {
   "id":"<CORE_CLIENT_ID_3>",
   "name":"meaningful_name",
   "feed":"wss://api.qredo.network/api/v1/p/coreclient/CORE_CLIENT_ID_3/feed",
   "timestamp":1607945099
   }
   ]
   }
```

## Core Client Websocket Feed

The WebSocket API offers real-time streaming event updates. All 'Actions' performed by the Signing Agent (fka Core Client)s are viewable and actionable. 

Using your chosen programming environment, listen to the Signing Agent (fka Core Client)'s WebSocket feed URL (returned in the previous step) 

```
wss://api.qredo.network/api/v1/p/coreclient/<CORE_CLIENT_ID>/feed
```

### Action Object

Attribute | Description |Child Attributes
-------|-------|----
id | Action ID|-
type | Action Type *ApproveWithdraw, ApproveTransfer, RejectWithdraw, RejectTransfer*|-
status | The status of the action *pending, expired, approved, rejected* |-
timestamp | Time in epoch |-
expireTime | Time in epoch |-
details | Details of the Action *ActionDetailApproveWithdraw, ActionDetailApproveTransfer*| txtype, txID, asset, amount, fees, net amount, reference, benefitOf, accountNo, companyID, fundID, initiatedtime, expires, recipientAddress, recipientAddressName, initiatedBy, initiatorName, initiatorType


### Create a Websocket Connection

You must include the following headers:

```
"x-api-key": "your API key",
"x-sign": "the signature of the timestamp concatenated with the WSS URL",
"x-timestamp": "the timestamp used to generate the signature"
```

#### Example using Golang

Use our example Golang **[partnerapi-sign](https://github.com/qredo/partner-api-sign)** client program.

Required: Golang

**Steps**

1) Clone the [Partner API Sign repo](https://github.com/qredo/partner-api-sign)

2) Change directory to the location where the repo is installed on your laptop.

```
cd partnerapi-sign
```

3) Build the sample app

```
go build -o partnerapi-sign
```

4) Listen to the WebSocket with the following command:

**Linux/Mac**

```
./partnerapi-sign websocket -url wss://api.qredo.network/api/v1/p/coreclient/<CORE_CLIENT_ID>/feed
```

**Windows**

```
partnerapi-sign.exe websocket -url wss://api.qredo.network/api/v1/p/coreclient/<CORE_CLIENT_ID>/feed
```

5) The command displays your URL, hash, signature and timestamp. It then attempts to connect and if successful returns "connected". Any actions sent to your Signing Agent (fka Core Client) are displayed in compressed JSON format. 

[View Signing Agent (fka Core Client) Actions](/#core-client-actions).

#### Example using Python3

Required: Python >= 3.6.1
Also, install the `websocket` library.

```
pip3 install websocket
```

**Steps**

1) Run the example Python script:

{% callout type="note" %}

You may have to switch the code tab to 'Python' in order to view it.

{% /callout %}

* EXAMPLE PYTHON

```python
from websocket import create_connection
ws = create_connection("wss://api.qredo.network/api/v1/p/coreclient/<CORE_CLIENT_ID>/feed", header={"x-api-key": "<YOUR_API_KEY>","x-sign": "<SIGNATURE>", "x-timestamp": "12345678"})
while True:
    result =  ws.recv()
    print (result)
```

2) You are successful when your CLI displays actions from the Signing Agent (fka Core Client) in compressed JSON format. 

##### Example WebSocket response

* **id**: Action ID
* **type**: Action Type e.g. ApproveWithdraw, ApproveTransfer
* **status**: The status of the action e.g. pending, expired, approved, rejected
* **timestamp**: Time in epoch
* **expireTime**: Time in epoch

* EXAMPLE WEBSOCKET RESPONSE

```json
{"id":", "<ACTION_ID>": ", "type": "<TYPE_OF_ACTION>", "status": ", "timestamp": , "expireTime": }
```
### Retrieve details of all pending actions

Retrieve details of all pending actions associated with a Signing Agent (fka Core Client).

`GET /coreclient/{client_id}/actions`

Parameters | Description
---------- | ---------
client_id |Required. String - This is the Core Client ID. 

Returns a list of pending actions. 

* EXAMPLE PENDING ACTIONS RESPONSE

```json
{
  "count": 0,
  "actions": [
    {
      "id": "<CORE_CLIENT_ID>",
      "type": "ApproveTransfer",
      "status": "pending",
      "timestamp": 1601972322,
      "expireTime": 1601973322
    }
  ]
}
```

### Retrieve details of an action

Retrive details of a specific action associated with a Signing Agent (fka Core Client)

`GET /coreclient/{client_id}/action/{action_id}`


Parameter | Description
-----|-----
action_ID | The unique identifier for the Action.
client_ID | The Core Client unique identifier.

Returns an Action object. 

* EXAMPLE ACTION OBJECT RESPONSE

```json
{
  "id": "<ACTION_ID>",
  "type": "ApproveWithdraw",
  "status": "pending",
  "timestamp": 0,
  "expireTime": 0,
  "details": {
    "txType": "string",
    "txID": "string",
    "asset": "string",
    "amount": 0,
    "fees": 0,
    "netAmount": 0,
    "reference": "string",
    "benefitOf": "string",
    "accountNo": "string",
    "companyID": "string",
    "fundID": "string",
    "initiatedTime": 0,
    "expires": 0,
    "recipientAddress": "string",
    "recipientAddressName": "string",
    "initiatedBy": "string",
    "initiatorName": "string",
    "initiatorType": "string"
  }
}
```

### Approve an action

Approve an action. 

`PUT /coreclient/{client_id}/action/{action_id}`


Parameter | Description
-----|-----
action_ID | The unique identifier for the Action.
client_ID | The Signing Agent (fka Core Client) unique identifier.

Returns an Action object with status "approved".  

### Reject an action

Rejects an action. 

`DELETE /coreclient/{client_id}/action/{action_id}`

Parameter | Description
-----|-----
action_ID | The unique identifier for the Action.
client_ID | The Core Client unique identifier.

Returns an Action object with status "rejected".  

## Holdings

A Holding represents a portfolio of Funds.

You can retrieve all assets held by a company according to the asset type. Each Holding entry includes the balance for that asset allowing you to track funds coming in and out.

### Holdings Object

Attribute|Description
-----|-----
total_count | This is the number of asset types held by a company
symbol | This is the symbol for the asset type e.g. BTC
amount | The quantity of each asset

### Retrieve Holdings

Use this endpoint to retrieve information on all assets held by a company on the Qredo Network.

`GET https://api.qredo.network/api/v1/p/company/{company_id}/holding`


Parameters | Description
----------|---------
company_id*| The unique identifier for the company

Returns the Holdings Object.

* EXAMPLE RETRIEVE HOLDINGS RESPONSE

```json
  "total_count": 3,
  "holdings": [
    {
      "code": "BTC",
      "amount": 4266
    },
    {
      "code": "ETH",
      "amount": 1077
    },
    {
      "code": "USDT",
      "amount": 500451
    }
  ]
}
```
## Deposits

Deposits add assets to Wallets contained within funds. 

You can add a deposit to a Wallet using the **wallet_code** or **wallet_id**. 

### Retrieve Fund Deposit Address

* EXAMPLE FUND DEPOSIT ADDRESS RESPONSE

```json
{
  "total_count": 0,
  "list": [
    {
      "asset": "BTC-TESTNET",
      "address": "n2GoFtw8aTH6y...fxrSp3iTL6jwUX",
      "balance": 100
    },
    {
      "asset": "ETH-TESTNET",
      "address": "y2koFtw8aTH6y...ljhrSp4trys456",
      "balance": 200
    }
  ]
}
```
Use this endpoint to retrieve fund deposit addresses for each asset in that fund.

`GET https://api.qredo.network/api/v1/p/company/{company_id}/fund/{fund_id}/deposit`

Parameters | Description
-----------|------------
company_id | Required. The unique company identifier
fund_id | Required. The unique fund identifier

Returns a list of fund assets, deposit addresses and current balances. See 'Example Fund Deposit Address Response'.

{% callout type="note" %}

Each asset has its own deposit address.

{% /callout %}

### Deposit assets into a Fund

Use this endpoint to add an asset to an existing fund. 

`POST https://api.qredo.network/api/v1/p/company/{company_id}/fund/{fund_id}/deposit`

Parameters | Description
-----------|------------
company_id | Required. The unique company identifier
fund_id | Required. The unique fund identifier

Returns a standard HTTP 200 OK Response indicates an **asset** has been added successfully.

## Funds

A Fund represents a container for a portfolio of **Assets** and their associated **Wallets**. Multiple Funds can be created on behalf of a customer to form a **Holding**. 

Administrators, Principals and Custody Groups are assigned to each individual fund. There is only one Custody Group per Fund.

Each Asset Type within a fund e.g. BTC can have multiple Wallets.

Wallets can be STANDARD, DEDICATED or MULTI-COUNTERPARTY. View [WALLET TYPES](#wallet-types).


### Fund Object

* EXAMPLE FUND OBJECT

```json
{
"fund_id":"EXAMpleFunD00JDaDfnT9sKNdY6",
"name":"Fund 1",
"description":"Fund for demonstration",
"custodygroup_withdraw":"EXAMpleGr0upjAymQVOroGre7DF",
"custodygroup_tx":"EXAMpleGr0upsjzO0tPZPQZyPW8",
"members":
  [
    {
      "entity":
      {
        "id":"EXAMpl31Dp8g8eoI0qBrNjvjFb4",
        "accountCode":"EXAMpl34cc0UNTbrRJtHEWwaZ2V785R5AJ13SCPPoDa8",
        "name":"Cryptomagic",
        "initials":"C",
        "type":"company",
        "company":
          {
            "name":"Cryptomagic"
          }
      },
        "permissions":
          {
            "admin":true
          }
            }
    ],
    "assets":["BTC-TESTNET"],
    "wallets":
    [
      {
        "wallet_id":"EXAMpl31DsKKm175EwASd7BKCQcwZ6QF5Mm8ozbAYb7H",
        "name":"New wallet",
        "asset":"BTC-TESTNET",
        "type":0,
        "status":"ready",
        "address":"EXAMpl3aDDr3ssW1xySNDcQ8VScLLA2Meg",
        "address_type":"P2PKH",
        "short_code":"purchase version cousin",
        "balance":0,
        "custodygroup_withdraw":"EXAMpleGr0upjAymQVOroGre7DF",
        "custodygroup_tx":"EXAMpleGr0upsjzO0tPZPQZyPW8"
        }
    ]
  }
```

Attribute|Description|Child Attributes
-----|-----|------
**fund_id**| The unique ID for the fund. Returned when fund is created. |-
**name**| This is the name allocated when fund created| -
**description** | This is a description of the fund| -
**custodygroup_withdraw** | The Fund Custody Policy specifying:  **members (trusted_party_id)** **account code**  **threshold** (number of signatures required) for authorization | -
**custodygroup_tx** | The Fund Transfer Policy specifying:  **members (trusted_party_id)**  **threshold** (number of signatures required) |- 
**members**| Details about Fund members and permissions | **entity ID** e.g. company ID  **account code** --  **name** e.g. company name  **initials** initials of entity name  **type** user - (firstName,lastName, email, userName), company - (company name), Core Client  **permissions** admin, withdraw, transfer or swap
**Wallets**| This defines the Wallet type(s) |**name** The name for a Wallet e.g. BTC Wallet   **asset** The asset associated with the Wallet. OPTIONAL **custodygroup_withdraw** Use to define a custom policy at Wallet level OPTIONAL **custodygroup_tx** Use to define a custom policy at Wallet level  **type**  *0 - Standard Wallet (For independent transactions)*  *1 - Dedicated Wallet (For use with a single 3rd Party)*  *3 - Multi-Counterparty Wallet (For use with several 3rd Parties)*  **connect** External 3rd Party connection requires counterparty_id and client_id (provided by third party)  **status** 


### Create Fund

#### FUND WITH TWO WALLETS

1. **Standard Wallet** - Inherit Fund TX Policy, Custom Withdrawal Policy

2. **Dedicated Wallet** - One Counterparty e.g. Exchange

* EXAMPLE REQUEST

```json
{
  "name": "Apollo",
  "description": "Fund with Dedicated & Standard Wallet - Inherit Fund TX Policy and Custom Withdrawal Policy",
  "custodygroup_withdraw": {
    "threshold": 1,
    "members": [
      "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
      "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt",
      "EXAMpleGr0uprWWQiPH3XkfJ5URjSHrhYk5zT6yeEEnU",
      "EXAMpleGr0upBNhnRGUuknkt1wBbjsEYBb8m6vhnsgak"
    ]
  },
  "custodygroup_tx": {
    "threshold": 1,
    "members": [
      "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
      "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt",
      "EXAMpleGr0uprWWQiPH3XkfJ5URjSHrhYk5zT6yeEEnU",
      "EXAMpleGr0upBNhnRGUuknkt1wBbjsEYBb8m6vhnsgak"
    ]
  },
  "wallets": [
    {
      "name": "Standard Wallet",
      "asset": "ETH",
      "custodygroup_withdraw": {
        "threshold": 2,
        "members": [
          "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
          "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt"
                   ]
                               }
    },
    {
      "name": "Dedicated Exchange Wallet",
      "asset": "BTC",
      "type": 1,
      "connect": [
        {
          "counterparty_id": "EXAMpl31D8rXq5TV5V8mdv9HxD2d8fyJqpDNH3MHT2pK",
          "client_id": "A12345"
        }
                ]
    }
  ]
}
```
* SUCCESSFUL 200 RESPONSE BODY

```json
{
  "fund_id":"EXAMpleFunD00JDaDfnT9sKNdY6",
  "custodygroup_withdraw":"EXAMpleGr0upjAymQVOroGre7DF",
  "custodygroup_tx":"EXAMpleGr0upsjzO0tPZPQZyPW8"
}
```

#### FUND WITH STANDARD WALLET - INHERIT TX POLICY, CUSTOM WITHDRAWAL POLICY

* EXAMPLE REQUEST

```json
{
    "name": "Apollo",
    "description": "Fund with Standard Wallet - Inherit Default Fund TX & Withdrawal Policies ",
    "custodygroup_withdraw": {
        "threshold": 1,
        "members": [
            "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
            "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt",
            "EXAMpleGr0uprWWQiPH3XkfJ5URjSHrhYk5zT6yeEEnU",
            "EXAMpleGr0upBNhnRGUuknkt1wBbjsEYBb8m6vhnsgak"
        ]
    },
    "custodygroup_tx": {
        "threshold": 1,
        "members": [
            "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
            "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt",
            "EXAMpleGr0uprWWQiPH3XkfJ5URjSHrhYk5zT6yeEEnU",
            "EXAMpleGr0upBNhnRGUuknkt1wBbjsEYBb8m6vhnsgak"
        ]
    },
    "wallets": [
    {
        "name": "Standard Wallet",
        "asset": "ETH",
        "custodygroup_withdraw": {
            "threshold": 1,
            "members": [
                "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
                "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt"
            ]
        }
    }
  ]
}
```
* SUCCESSFUL 200 RESPONSE BODY

```json
{"fund_id":"EXAMpleFunD00iKfG9votdTExMg","custodygroup_withdraw":"EXAMpleGr0up9UC3hXBYRNq1ynB","custodygroup_tx":"EXAMpleGr0upPXx9mcNb6rOrmNj"}
```

#### FUND WITH STANDARD WALLET - INHERIT DEFAULT FUND TX & WITHDRAWAL POLICY

* EXAMPLE REQUEST

```json
{
    "name": "Apollo",
    "description": "Fund with Standard Wallet - Inherit both Default Fund TX & Withdrawal Policies ",
    "custodygroup_withdraw": {
        "threshold": 1,
        "members": [
            "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
            "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt",
            "EXAMpleGr0uprWWQiPH3XkfJ5URjSHrhYk5zT6yeEEnU",
            "EXAMpleGr0upBNhnRGUuknkt1wBbjsEYBb8m6vhnsgak"
        ]
    },
    "custodygroup_tx": {
        "threshold": 1,
        "members": [
            "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
            "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt",
            "EXAMpleGr0uprWWQiPH3XkfJ5URjSHrhYk5zT6yeEEnU",
            "EXAMpleGr0upBNhnRGUuknkt1wBbjsEYBb8m6vhnsgak"
        ]
    },
    "wallets": [{
            "name": "Standard Wallet",
            "asset": "ETH"
        }
    ]
}
```
* SUCCESSFUL 200 RESPONSE BODY

```json
{
"fund_id":"EXAMpl31Dk7uJYuugG63Uv2dQrW",
"custodygroup_withdraw":"EXAMpleGr0up6wS9HJwALHpIcjG",
"custodygroup_tx":"EXAMpleGr0upCv1RDZY0m1iGmCH"
}
```

#### FUND WITH TWO STANDARD WALLETS

1. Standard Wallet containing BTC, Custom Withdraw Policy & Default TX Policy

2. Standard Wallet containing ETH, Custom Withdraw Policy and Custom TX Policy

* EXAMPLE REQUEST

```json
{
    "name": "Apollo",
    "description": "Fund with Standard Wallet - Custom TX & Custom Withdrawal Policies",
    "custodygroup_withdraw": {
        "threshold": 1,
        "members": [
            "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
            "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt",
            "EXAMpleGr0uprWWQiPH3XkfJ5URjSHrhYk5zT6yeEEnU",
            "EXAMpleGr0upBNhnRGUuknkt1wBbjsEYBb8m6vhnsgak"
        ]
    },
    "custodygroup_tx": {
        "threshold": 1,
        "members": [
            "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
            "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt",
            "EXAMpleGr0uprWWQiPH3XkfJ5URjSHrhYk5zT6yeEEnU",
            "EXAMpleGr0upBNhnRGUuknkt1wBbjsEYBb8m6vhnsgak"
        ]
    },
    "wallets": [{
            "name": "Standard Wallet with BTC, Custom TX Policy & Default Withdrawal Policy",
            "asset": "BTC",
            "custodygroup_tx": {
                "threshold": 2,
                "members": [
                    "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
                    "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt"
                ]
            }
        },
        {
            "name": "Standard Wallet with ETH, Custom TX Policy & Custom Withdrawal Policy",
            "asset": "ETH",
            "custodygroup_withdraw": {
                "threshold": 3,
                "members": [
                    "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt",
                    "EXAMpleGr0uprWWQiPH3XkfJ5URjSHrhYk5zT6yeEEnU",
                    "EXAMpleGr0upBNhnRGUuknkt1wBbjsEYBb8m6vhnsgak"
                ]
            },
            "custodygroup_tx": {
                "threshold": 2,
                "members": [
                    "EXAMpleGr0upUDDtHxofpunmPibmAJ7mjTSMDchk2eoq",
                    "EXAMpleGr0upXy6W46AxeFURfuWDLYXKod2ZhvrmvNyt",
                    "EXAMpleGr0uprWWQiPH3XkfJ5URjSHrhYk5zT6yeEEnU",
                    "EXAMpleGr0upBNhnRGUuknkt1wBbjsEYBb8m6vhnsgak"
                ]
            }
        }
    ]
}  
```

* SUCCESSFUL 200 RESPONSE BODY

```json
{
  "fund_id":"EXAMpleFunD008AMe2t9Ay3dOVi",
  "custodygroup_withdraw":"EXAMpleGr0upel1Og6UJ1jsmYLn",
  "custodygroup_tx":"EXAMpleGr0upxqsgcfp2Po9T87C"}
```

Use this endpoint to create a fund and specify what assets you want in it.

`POST https://api.qredo.network/api/v1/p/company/{company_id}fund`

Parameters| Description
-----------|---------
company_id| Required. The unique company identifier
name| Name for the Fund.
description| Fund description
custodygroup_withdraw| Fund Custody Policy (withdrawals) **threshold** (number of custodial signatures required), each custodial **member**(trusted_entity_ids)
custodygroup_tx | Fund Custody Policy (transfers) **threshold** (number of custodial signatures required), each custodial **member**  (trusted_entity_ids)
wallets | **name** Wallet name,  **asset** Asset type e.g. BTC-Testnet,  **custodygroup_withdraw**


### Retrieve Fund

* EXAMPLE RETRIEVE FUND RESPONSE

```json
{
  "fund_id": "EXAMpl31D5hMOVjGFl1DrsbnvmS",
  "name": "Fund 1",
  "description": "Fund for demonstration purposes",
  "custodygroup_withdraw": "EXAMpleGr0up89dcdhue6LJaFUS",
  "custodygroup_tx": "EXAMpleGr0upV0jggAv1mFSR9jp",
  "members": {
    "entity": {
      "id": "EXAMpl31D2HtHPmWDUKWJ4HR0xp",
      "accountCode": "EXAMpl34cc0UNTmYxB1NeoAVhgwiEHvAoehhYYwZkAYU",
      "name": "ACME Corp",
      "initials": "A",
      "type": "company",
      "company": {
        "name": "ACME Corp"
      }
    },
    "permissions": {
      "admin": true
    }
  },
  "assets": [
    "BTC-TESTNET"
  ],
  "wallets": [
    {
      "wallet_id": "5Hb8J...WCZES",
      "name": "New wallet",
      "asset": "BTC-TESTNET",
      "type": 213312000,
      "status": "ready",
      "address": "EXAMpleTmzkd7bRKKsUmjucpLQLThv8eYX",
      "address_type": "P2PKH",
      "short_code": "bu**y h**sh stic*",
      "balance": 0,
      "custodygroup_withdraw": "EXAMpleGr0up89dcdhue6LJaFUS",
      "custodygroup_tx": "EXAMpleGr0upV0jggAv1mFSR9jp"
    },
    {
      "wallet_id": "7MkAB...S1xH6",
      "name": "New dedicated wallet",
      "asset": "BTC-TESTNET",
      "type": 1,
      "status": "ready",
      "address": "EXAMpleYSdN7uow8MJWCCffBaAQsrqW217",
      "address_type": "P2PKH",
      "short_code": "fa***ue in***t con**ct",
      "balance": 1000000,
      "custodygroup_withdraw": "EXAMpleGr0up89dcdhue6LJaFUS",
      "custodygroup_tx": "EXAMpleGr0upV0jggAv1mFSR9jp",
      "connected": {
        "counterparty_id": "EXAMpl31D9ikHHDVdjZNXjrt3CzGg4HvXRbw94qmqjVm",
        "name": "Test Exchange",
        "client_id": "A12345",
        "status": "connected"
      }
    }
  ]
}
```

Use this endpoint to retrieve information about a specific Fund including custody groups and members. 

`GET https://api.qredo.network/api/v1/p/company/{company_id}/fund/{fund_id}`


Parameters| Description
----|-----
company_id | Required. The unique company identifier
fund_id | Required. The unique fund identifier

Returns details about a specific fund, custody groups for withdrawals and transfers as well as details on fund members and their permissions.

### Search Funds

* EXAMPLE FUND SEARCH RESPONSE

```json 
{
  "total_count": 2,
  "list": [
    {
      "name": "Big Fund",
      "id": "1dnpG2K2A......Fl1DrsbnvmS"
    },
    {
      "name": "Big Load",
      "id": "2cnp......VjGFl1DrsbnvmN"
    }
  ]
}
```

Use this endpoint to search all Company funds that match a query.

`GET https://api.qredo.network/api/v1/p/company/{company_id}/fund/search`

Parameters| Description
----|-----
company_id* | Required. The unique company identifier
query | String of 3 letters or more. 

Returns all matching **fund names** and their corresponding **fund_id** are returned. See 'Example Fund Search Response'.


## Wallets

### Wallet Types

Granular Custody Policies are available at Wallet level. 
Multiple Asset Types can be added to a fund. Multiple wallets can be associated with individual Asset Types. 

Parameter | Wallet Type| Description
----|----|-----
| 0 | Standard Wallet | To conduct transactions independently.
| 1 | Dedicated Wallet | Solely for access by a single third party such as an Exchange. The third party is a Custodian over the wallet and must approve assets moving out of it. This is required because the trader may for example have a limit order open with a third party so they must ensure funds are available to meet the limit order. The connected third party also has the right to 'sweep' assets out of the wallet without further approval from the wallet's owners or custodians.
| 3 | Multi-Counterparty | Accessible by multiple third parties. The purpose of this Wallet is to enable a trader to have multiple exchanges connected to the same Wallet for example to allow market orders to be placed on multiple exchanges. The connected third party/parties also have the right to 'sweep' assets out of the wallet without further approval from the wallet's owners or custodians.


Returns a **fund id**, **custodygroup_withdraw** policy ID, **custodygroup_tx** policy ID

* EXAMPLE CREATE FUND RESPONSE

```json
{
  "fund_id": "EXAMpleFunD0zasqtmtTXXoaNNJ",
  "custodygroup_withdraw": "EXAMpleGr0upScmInkMXSPzitj5",
  "custodygroup_tx": "EXAMpleGr0upPs2v5kvxglwKTKV"
}
```

### Create Wallet

Use this endpoint to add a new wallet to a fund and specify what assets you want in it.

The default fund custody policy is used unless a wallet-level custody policy is specified.proto

`POST https://api.qredo.network/api/v1/p/company/{company_id}fund/{fund_id}/wallet`

Parameters| Description
----|-----
company_id| Required. The unique company identifier
fund_id| Required. The identifier for the fund.
name| Name for the Fund.
description| Fund description
custodygroup_withdraw| Optional. Fund Custody Policy (withdrawals) **threshold** (number of custodial signatures required), each custodial **member**(trusted_entity_ids)
custodygroup_tx | Optional. Fund Custody Policy (transfers) **threshold** (number of custodial signatures required), each custodial **member**  (trusted_entity_ids)
wallets | **name** Wallet name,  **asset** Asset type e.g. BTC-Testnet,  **custodygroup_withdraw**

* EXAMPLE CREATE WALLET REQUEST

```json
{
  "wallets": [
    {
      "name": "New wallet",
      "asset": "BTC-TESTNET"
    },
    {
      "name": "New wallet with custom custody group",
      "asset": "BTC-TESTNET",
      "custodygroup_withdraw": {
        "threshold": 1,
        "members": [
          "EXAMpleGr0upzasqtmtTXXoaNNJ",
          "EXAMpleGr0upsdg4akvxglwKTKV"
        ]
      },
      "custodygroup_tx": {
        "threshold": 1,
        "members": [
          "EXAMpleGr0upzasqtmtTXXoaNNJ",
          "EXAMpleGr0upsdg4akvxglwKTKV"
        ]
      }
    },
    {
      "name": "New dedicated wallet",
      "asset": "BTC-TESTNET",
      "type": 1,
      "connect": [
        {
          "counterparty_id": "6WM25...mqjVm",
          "client_id": "A12345"
        }
      ]
    }
  ]
}
```

Returns wallet IDs.

* EXAMPLE CREATE WALLET RESPONSE

```json
{
  "wallets": [
    "EXAMpl31DZ9Voz9EAbtTHk7A2rhQra4aPcscYNLGePci",
    "EXAMpl31DTHk7A2rhQgmTZYNLGePcira4aPcscKGMWoE"
  ]
}
```

### Retrieve Wallet

* EXAMPLE RETRIEVE WALLET REQUEST

Use this endpoint to retrieve information about a specific wallet including balance, addresses and custody groups. 

`GET https://api.qredo.network/api/v1/p/wallet/{wallet_id}`


Parameters| Description
----|-----
wallet_id | Required. The unique wallet identifier (returned when the wallet was created)

```json
{
  "wallet_id": "string",
  "name": "string",
  "asset": "string",
  "type": 0,
  "status": "string",
  "address": "string",
  "address_type": "string",
  "short_code": "string",
  "balance": 0,
  "custodygroup_withdraw": "string",
  "custodygroup_tx": "string",
  "policy_withdraw": {
    "id": "string",
    "name": "string",
    "threshold": 0,
    "members": [
      {
        "id": "string",
        "name": "string",
        "accountCode": "string",
        "initials": "string",
        "type": "user",
        "company": {
          "name": "string"
        },
        "user": {
          "firstName": "string",
          "lastName": "string",
          "email": "string",
          "userName": "string"
        },
        "core-client": {
          "name": "string"
        }
      }
    ]
  },
  "policy_tx": {
    "id": "string",
    "name": "string",
    "threshold": 0,
    "members": [
      {
        "id": "string",
        "name": "string",
        "accountCode": "string",
        "initials": "string",
        "type": "user",
        "company": {
          "name": "string"
        },
        "user": {
          "firstName": "string",
          "lastName": "string",
          "email": "string",
          "userName": "string"
        },
        "core-client": {
          "name": "string"
        }
      }
    ]
  },
  "connected": [
    {
      "client_id": "string",
      "counterparty_id": "string",
      "name": "string",
      "status": "string"
    }
  ]
}
```

## TRANSACTIONS

Transaction types can be **deposit, withdraw, transferIn, transferOut, swap, swapIn, swapOut**

## Transfers

A Transfer object is created when you move assets from a fund to a counterparty fund on the Qredo Network. All transfers require Custodial Approval (in accordance with the fund transfer policy).

### The Transfer object

Attributes| Description | Child Attributes
---------|------------|-----
**wallet_id**| Required. String - The originator wallet ID |-
**counterparty_wallet_address**| Required. String - This is the Counterparty Wallet Address NOTE: You can use the Counterparty **Wallet code** or their wallet_id |-
**reference** | String - This is your reference for your own records.|-
**benefit_of**| String - This is the name of the recipient of the transfer.|-
**account_no**| String - (Where relevant) This is the account number of the recipient of the transfer. |-
**expires** | Required. String - This is the time in epoch when the transfer expires without custodial approval.|-
**send*** | Required. This is the type and amount of asset sent.|**symbol** - Required. String - Asset Symbol e.g. BTC
 | |  **amount** - Required. String - Amount of asset sent.

### Create Transfer

* EXAMPLE TRANSFER REQUEST

```json
curl -X POST "https://api.qredo.network/api/v1/p/company/<COMPANY_ID/transfer" 
-H "Content-Type: application/json"
-H "accept: application/json" \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
-d "
{
  "wallet_id": "HUnmE...gRKMk",
  "counterparty_wallet_address": "D1h4c...6QuSP",
  "expires": 1640998800,
  "benefit_of": "Malala Yousafzai",
  "account_no": "123-XX",
  "reference": "97879780",
  "send": {
    "symbol": "BTC",
    "amount": 800
  }
}"
```

Use this endpoint to create a transfer: 

`POST /company/{company_id}/transfer`

Parameters | Description
-----------|------------
wallet_id* | Required. The originator wallet_id
counterparty_wallet_address* | Required. This is the **wallet code** OR counterparty **wallet_id** of the counterparty.
expiration* | Required. This is the time in epoch when the transfer expires without custodial approval. 
send symbol* | Required. This is the symbol for the asset being transferred.
send amount* | Required. This is the quantity of the asset being sent. 
reference | Optional
benefit_of | Optional
account_no | Optional

See 'Example Transfer Request'. 

Returns  a Transaction ID **tx_id**.

* EXAMPLE TRANSFER RESPONSE

```json
{
  "tx_id": "9827feec4e......a7c3b97add"
}
```

## Addresses

### Add Whitelist Address

Use this endpoint to whitelist an address. This is essential to ensure that a withdrawal transaction can be approved using this address.

`GET https://api.qredo.network/api/v1/p/company/{company_id}/fund/{fund_id}/whitelist`

### Retrieve Fund Whitelist

Use this endpoint to get a list of all whitelisted addresses associated with a specific fund. 

`GET https://api.qredo.network/api/v1/p/company/{company_id}/fund/{fund_id}/whitelist`


Parameters|Description
---------|------------
company_id*| Required. The unique company identifier
fund_id* | Required. The unique fund identifier
address | Optional. Find out whether a specific address has been whitelisted
asset | Optional. Filter addresses for a specific asset

Returns all whitelisted addresses for that fund.

* EXAMPLE FUND WHITELIST RESPONSE

```json
{
  "total_count": 2,
  "list": [
    {
      "name": "Rosa_wallet",
      "asset": "BTC-TESTNET",
      "address": "bc1qg...ktq98"
    },
    {
      "name": "Alice_wallet",
      "asset": "BTC",
      "address": "tb1qd...x7ud8"
    }
  ]
}
```

## Withdrawals

A Withdraw Object is created when you move assets from a fund to a withdrawal address. All withdrawals require Custodial Approval (in accordance with the fund withdrawal policy).

### Withdraw Object

Attributes|Description|Child Attributes
----------|-----------|--------------
**wallet_id**| Required. String - The originator wallet
**address**| Required. String - The destination address. This must be whitelisted.
**expires** | Required. String - This is the time in epoch when the withdrawal expires without custodial approval.|-
**reference** | String - This is your reference for your own records.|-
**benefit_of**| String - This is the name of the recipient of the withdrawal.|-
**send*** | Required. This is the type and amount of asset sent.|**symbol** - Required. String - Asset Symbol e.g. BTC
 | |  **amount** - Required. String - Amount of asset sent. 

### Create withdraw

* EXAMPLE WITHDRAW REQUEST

```curl
curl -X POST "https://api.qredo.network/api/v1/p/company/<COMPANY_ID>/withdraw" 
-H "accept: application/json" \
-H  "Content-Type: application/json"  \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
-d "
"wallet_id":"<ORIGINATOR_WALLET_ID>",
"address":"mkLaDxYkiaYW...TNu9ZcpHgd6",
"expires":16****800,
"benefit_of":"Bob Cohen",
"account_no":"123-XX",
"reference":"CX15R99XX",
"send":{
  "symbol":"BTC",
  "amount":800}
  }"
```

Use this endpoint to create a withdrawal: 

`POST /company/{company_id}/withdraw`

Parameters | Description
-----------|------------
company_id* | Required. The unique company identifier
wallet_id* | Required. The originator fund identifier
address* | Required. The destination address
expires* | Required. This is the time in epoch when the transfer expires without custodial approval. 
send symbol* | Required. This is the symbol for the asset being transferred.
send amount* | Required. This is the quantity of the asset being sent. 
reference | Optional
benefit_of | Optional
account_no | Optional

See 'Example withdraw request' 

#### Response

* EXAMPLE WITHDRAW RESPONSE

```json
{
  "tx_id": "45677feec4e......a7c3b97add"
}
```

The Response is a Transaction ID **tx_id**.

## Atomic Swaps

Atomic Swaps enable you to exchange assets with another member on the Qredo Network in a decentralized way with granular governance as defined by you. 

{% callout type="note" %}

The member you want to trade with must have a fund containing both assets you want to swap with (e.g. ETH-TESTNET & BTC-TESTNET)

{% /callout %}

1. A Maker posts their quote. 
2. The quote is authorized by the Maker's Custodians.
3. The quote is added to the **Liquidity Hub** if add_to_liquidity_hub is set to 'True'
4. A potential Taker views quotes on the Liquidity Hub
5. A Taker uses the swap transaction_id to take the quote. 
6. The quote is authorized by the Taker's Custodians
7. The Atomic swap completes in a single transaction. 

### Create Atomic Swap (Maker)


* EXAMPLE ATOMIC SWAP MAKE REQUEST

```curl
curl -X POST "https://api.qredo.network/api/v1/p/company/<MAKER_COMPANY_ID>/atomicswap/make" 
-H "accept: application/json" \
-H  "Content-Type: application/json"  \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
-d "
{
  "send_wallet_id": "nLQtaZgx49s2sa7dD1K8unLQtaZgx49s",
  "receive_wallet_id": "2sa7dD1K8unLQtaZgx49snLQtaZgx49s",
  "expires": 1640998800,
  "reference": "CX15R99XX",
  "send": {
    "symbol": "BTC",
    "amount": 800
  },
  "receive": {
    "symbol": "ETH",
    "amount": 2536
  },
  "add_to_liquidity_hub": false
}
```
* EXAMPLE ATOMIC SWAP MAKE RESPONSE

```json
{
  "tx_id": "bda3daa7c3...eec4eae4e80",
  "tx_url": "https://qredo.network/aswp<tx_id>"
}
```
**Prerequisites:**

1. Select a fund with required assets and quantities to swap with. (fund_id)

2. Make your quote. Specify the the assets and quantities you want to send and receive. 

3. Set an expiry time for the swap. 

4. Use this endpoint to make a quote and initiate an Atomic Swap.

`POST https://api.qredo.network/api/v1/p/company/{company_id}/atomicswap/make`

Parameters | Description
-----------|------------
send_wallet_id* | Required. The Maker's wallet ID
receive_wallet_id* | Required. The Taker's wallet ID
expires* | Required. 
reference | Optional. Your reference for the swap
send* | Required. Your 'Sell' price - *Asset Symbol* and *Amount*
receive* | Required. Your 'Buy' price - *Asset Symbol* and *Amount*
add_to_liquidity_hub | Optional. True or False. If True, quote is automatically added to Liquidity Hub

See 'Example Atomic Swap Make Request'

Returns a unique Swap Transaction ID **tx_id** and Transaction URL **tx_url**.

{% callout type="warning" %}

This must be passed to the Taker and executed prior to the expiry time.

{% /callout %}

{% callout type="note" %}

The Atomic Swap Make Request must be authorized in accordance with the Maker's custodial policy.

{% /callout %}

{% callout type="note" %}

The tx_url can be sent to a 'real' user for them to take.

{% /callout %}

### View Atomic Swap Details

#### View Status of Make

Use the following endpoint to view transaction details of a Make swap. 

`GET https://api.qredo.network/api/v1/p/company/{company_id}/atomicswap/make/{tx_id}`

Parameters|Description
----------|-----------
company_id| Required. Makers unique company identifier
tx_id| Required. The Maker swap transaction_id 

Returns the Transaction Object. See 'Example Transaction Object'


### Delete Atomic Swap (Maker)

Use this endpoint to cancel an Atomic Swap you have created. This removes it from the Liquidity Hub. 

`DELETE https://api.qredo.network/api/v1/p/company/{company_id}/atomicswap/make/{tx_id}`

### Complete Atomic Swap (Taker)

* EXAMPLE ATOMIC SWAP TAKE REQUEST

```curl
curl -X POST "https://api.qredo.network/api/v1/p/company/{company_id}/atomicswap/take" 
-H "accept: application/json" \
-H  "Content-Type: application/json"  \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
-d "
{
  "swap_id": "EXAMpl31DhSEgjObPPHU7vwTNzb",
  "send_wallet_id": "EXAMpl31DKuXpb4hNIgp7DLWGRN",
  "receive_wallet_id": "EXAMpl31DunLQtaZgx49snLQtaZgx49s",
  "reference": "CX15R99XX"
}
```

1. Use the Swap transaction_id from the Maker to perform the Swap. 

2. View Atomic Swap Status to ensure it has been authorized

3. Select a fund with the required assets and quantities to receive and send. 

4. Use this endpoint to take the quote before it expires. 

{% callout type="note" %}

The Atomic Swap Take Request must be authorized in accordance with the Taker's Custodial Policy.

{% /callout %}

Parameters | Description
-----------|------------
swap_id* | Required. The tx_id (obtained from the Maker)
send_wallet_id* | Required. The Taker's wallet ID or wallet code
receive_wallet_id* | Required. The Maker's wallet ID or wallet code
reference* | Reference for the swap

See 'Example Atomic Swap Take Request'

Returns a unique Swap Transaction ID **tx_id**.

View **Transaction Status** using the **tx_id**

View **Holdings** to confirm the Swap has executed.

{% callout type="note" %}

The tx_url can be sent to a 'real' user for them to take.

{% /callout %}

### View Atomic Swap Status(Take)

Use the following endpoint to view transaction details of a Take swap. 

`GET https://api.qredo.network/api/v1/p/company/{company_id}/atomicswap/take/{tx_id}`

Parameters|Description
----------|-----------
company_id| Required. Takers unique company identifier
tx_id| Required. The Taker swap transaction_id 

Returns the Transaction Object. See 'Example Transaction Object'.

## Liquidity Hub

The Liquidity Hub is a place for makers to broadcast Atomic Swap quotes. It is also the place for potential takers to retrieve information about all available Atomic Swap quotes. 

### Add Atomic Swap

* EXAMPLE ATOMIC SWAP REQUEST

```
```curl
curl -X POST "https://api.qredo.network/api/v1/p/company/{company_id}/liquidityhub" 
-H "accept: application/json" \
-H  "Content-Type: application/json"  \
-H "x-timestamp: <Epoch timestamp>" \
-H "x-sign: <signature generated_using your RSA key>" \
-H "X-API-KEY: <API key created in Qredo web app>" \
-d "
{
  "tx_id": "1lm519eiGT......HrEYvSBuSn"
}
```
Use this endpoint to add your swap to the Liquidity Hub.

`POST https://api.qredo.network/api/v1/p/company/{company_id}/liquidityhub`

Parameters | Description
----------|------------
company_id | Required. The Maker's unique company identifier
tx_id | Required. The Transaction Id received after creating the swap. 

Returns a standard HTTP 200 OK Response to indicate a **swap_transaction_id** has been added successfully.

{% callout type="note" %}

The tx_id is not posted in the Liquidity Hub unless it has custodial approval.

{% /callout %}

### Retrieve all Active Swaps

Use this endpoint to retrieve all active swaps in the Liquidity Hub. 

`GET https://api.qredo.network/api/v1/p/company/{company_id}/liquidityhub`

Returns a list of active swaps. (Owner indicates whether the swap is associated with company_id queried).

* EXAMPLE GET RESPONSE LIQUIDITY HUB

```json
{"items":
[
  {
    "tx_id":"EXAMpl31D89jkYn", 
    "send_amount": 800,
    "send_asset": "ETH",
    "receive_amount": "BTC",
    "receive_asset": "ETH",
    "owner": false,
    "expires": 189896876,
    "added":198787878
  }
]
}
```

### Liquidity Hub WebSocket Feed 

Use this endpoint to get a real-time feed of all active swaps in the Liquidity Hub. 

`wss://api.qredo.network/api/v1/p/liquidityhub/feed`

## Fund Ledger

View all transactions for a Fund. 

### View Fund Ledger

* EXAMPLE LEDGER OBJECT

```json
{
  "List": [
    {
      "TxID": "EXAMpl31DpRzQk3HKnnebfCj5Dh",
      "chainTxID": "EXAMpl31D6wvpY7ioT35sKZ5kyqaSt6kLdTaSShZ11Gb",
      "fundID": "EXAMpl31DmuhNLFvVhwiiqj6XcM",
      "timestamp": 1605269988,
      "expireTime": 1640998800,
      "status": "approved",
      "type": "transferOut",
      "asset": "BTC-TESTNET",
      "amount": 2803,
      "fees": 3,
      "netAmount": 2800,
      "counterpartyID": "EXAMpl31DiseE7RfifsMCDusXL8",
      "counterpartyName": "ACME Corp",
      "address": "",
      "reference": "CX15R99XX",
      "benefitOf": "John Smith",
      "accountNo": "123-XX"
    },
    {
      "TxID": "",
      "chainTxID": "EXAMpl31DsvPjnBaZzaEunxQppdBayGW7KEd2Y8hWtQ8",
      "fundID": "EXAMpl31DmuhNLFvVhwiiqj6XcM",
      "timestamp": 1605267445,
      "expireTime": 0,
      "status": "",
      "type": "deposit",
      "asset": "BTC-TESTNET",
      "amount": 10000,
      "fees": 0,
      "netAmount": 10000,
      "counterpartyID": "",
      "counterpartyName": "",
      "address": "EXAMpl31DAeG9FFbigDyt3WqebrGohuemk",
      "reference": "",
      "benefitOf": "",
      "accountNo": ""
    }
  ]
}
```

Use the following endpoint to view all transations for a company fund. 

`GET https://api.qredo.network/api/v1/p/company/{company_id}/fund/{fund_id}/ledger`

Parameters|Description
----------|-----------
company_id| Required. company identifier
fund_id| Required. transaction_id (returned when the Fund was created)
asset| Asset type
wallet_id| Wallet Code
direction| Transaction direction  **all**  **in**  **out**

Returns the Ledger Object. See '*Example Ledger Object*'.


## Transaction Status

### View Transfer status

* EXAMPLE TRANSACTION OBJECT

```json
{
  "approvedCount": 1,
  "expires": 1640998800,
  "initiatedTimestamp": 1594281300,
  "initiatorAuthorized": true,
  "initiatorName": "Big Fund",
  "reference": "CX15R99XX",
  "status": [
    {
      "actionID": "EXAMpl31D6WrooxkquWmirbo4oL",
      "firstName": "Alice",
      "lastName": "Walker",
      "status": "approved",
      "timestamp": 1594281312,
      "userID": "<TRUSTED_ENTITY_ID>",
      "username": "awalker"
    }
  ],
  "statusDetails": {
    "accountNo": "123-XX",
    "amount": 801,
    "asset": "BTC-TESTNET",
    "benefitOf": "Bob Stander",
    "expires": 0,
    "fees": 1,
    "fundID": "<FUND_ID>",
    "netAmount": 800,
    "recipientAddress": "",
    "recipientEmail": "Bob@stander.org",
    "recipientFirstName": "Bob",
    "recipientLastName": "Stander",
    "recipientUsername": "bstander",
    "reference": "CX15R99XX",
    "requestedBy": "Big Fund"
  },
  "thresholdRequired": 1,
  "txID": "1eRPC44Mjh...jYio5ulu",
  "txStatus": "approved"
}
```

Use the following endpoint to view the status of a specific transfer for a company. 

`GET https://api.qredo.network/api/v1/p/company/{company_id}/transfer/{tx_id}`

Parameters|Description
----------|-----------
company_id| Required. company identifier
tx_id| Required. transaction_id (returned when the Transfer was created)

Returns the Transaction Object. See 'Example Transaction Object'.

### Transfer Status Codes

tx status | Description
-----------|------------
pending_initiate_transfer| String - The trade initiator is yet to authorise the transfer
initiate_rejected_transfer | String- The transfer has been rejected by the trade initiator.
pending_transfer | String - The transfer is yet to be approved by the custodians
expired_transfer | String - The time set for custodian approval has been exceeded. The time is set in Epoch.
approved_transfer | String - The transfer has been approved and has exceeded the threshold. The transfer is written to the Qredo Blockchain.
custody_rejected_transfer | String - The transfer has been rejected by the custodians.
chain_rejected_transfer | String - Transfer has been rejected by the Qredo blockchain. (Contact Support)

### View Withdraw status

Use the following endpoint to view the status of a specific withdraw for a company. 

`GET https://api.qredo.network/api/v1/p/company/{company_id}/withdraw/{tx_id}`

Parameters|Description
----------|-----------
company_id| Required. company identifier
tx_id| Required. transaction_id (returned when the Withdraw was created)

Returns the Transaction Object. See 'Example Transaction Object'

```json
{
  "approvedCount": 1,
  "expires": 1640998800,
  "initiatedTimestamp": 1594281300,
  "initiatorAuthorized": true,
  "initiatorName": "SW Corp",
  "reference": "CX15R99XX",
  "status": [
    {
      "actionID": "EXAMpl31D6WrooxkquWmirbo4oL",
      "firstName": "Han",
      "lastName": "Solo",
      "status": "approved",
      "timestamp": 1594281312,
      "userID": "EXAMpl31DsbSXBs1yjpzMaesirXPhqcz8kM7zgafmZef",
      "username": "hsolo"
    }
  ],
  "statusDetails": {
    "accountNo": "123-XX",
    "amount": 801,
    "asset": "BTC-TESTNET",
    "benefitOf": "Ben Kenobi",
    "expires": 0,
    "fees": 1,
    "fundID": "EXAMpl31DpocC9uRuy3DlNC7zXd",
    "netAmount": 800,
    "recipientAddress": "",
    "recipientEmail": "han@falcon.com",
    "recipientFirstName": "Han",
    "recipientLastName": "Solo",
    "recipientUsername": "hsolo",
    "reference": "CX15R99XX",
    "requestedBy": "SW Corp"
  },
  "thresholdRequired": 1,
  "txID": "EXAMpl31DhRI3sJ6UzzjYio5ulu",
  "txStatus": "approved"
}
```
### Withdrawal Status Codes

**Withdrawal Status Codes**

tx status | description
-----------|------------
pending_initiate_withdrawal | String - The trade initiator is yet to authorise the withdrawal.
initiate_rejected_withdrawal| String - The withdrawal has been rejected by the trade initiator.
pending_withdrawal | String - The withdrawal is yet to be approved by the custodians.
expired_withdrawal | String - The time set for custodian approval has been exceeded. The time is set in Epoch.
approved_withdrawal | String - The withdrawal has been approved and has exceeded the threshold. The withdrawal is written to the Qredo Blockchain.
custody_rejected_withdrawal | String - The withdrawal has been rejected by the custodians.
chain_rejected_withdrawal | String - Withdrawal has been rejected by the Qredo blockchain. (Contact Support)


## Core Client Actions

Action Type | Description
-----|-----
ApproveWithdraw | A withdraw has been approved.
ApproveTransfer | A transfer has been approved.

Action Status | Description
----------|----------
pending | The action has not yet initiated
expired | The action has expired
approved | The action has been approved
rejected | The action has been rejected. 

# THIRD PARTY CONNECTIONS

## Exchanges
[Dedicated](##wallet-types) and [Multi-Counterparty](#wallet-types) wallets can be used to connect with third party exchanges.

### Retrieve list of exchanges

Use the following endpoint to retrieve a list of exchanges to connect with.  

`GET https://api.qredo.network/api/v1/p/exchanges`

Returns a list of **Exchanges** and their details.

* EXAMPLE EXCHANGE LIST RESPONSE

```json
{
  "exchanges":
  [
    {
    "id":"EXAMpl31D8rXq5TV5V8mdv9HxD2d8fyJqpDNH3MHT2pK",
    "name":"Test Exchange",
    "url":"testexchange.co",
    "logourl":"https://www.logo.svg",
    "client":
      {
        "fields":
        [
          {
            "id":"customer_id",
            "type":"string",
            "name":"Your customer ID"
          },
          {
            "id":"customer_email",
            "type":"string",
            "name":"Your Email address"
          }
        ]
      }
    }
```

See examples of how to [Connect a Dedicated Wallet to an Exchange](#create-fund)


## Sweeps
Sweeps are the ability for specified users to transfer into or withdraw funds from a **Dedicated** or **Multi-Counterparty** wallet without further authorisation. Specified users are 3rd parties such as Exchanges.

### Add new Sweep

* EXAMPLE SWEEP REQUEST

```curl
{
  "wallet_id": "EXAMpl31DeNYj2pTH8DfikDcH5gjHqhj9iadgVQgRKMk",
  "recipient_wallet_id": "EXAMpl31DeNYj2pTH8DfikDcH5gjHqhj9iadgVQgRKMk",
  "expires": 1640998800,
  "reference": "CX15R99XX",
  "send": {
    "symbol": "BTC",
    "amount": 800
  }
}
```

Initiate a new sweep transaction and define parameters for the transaction.

Use the following endpoint to create a new sweep. 

`POST https://api.qredo.network/api/v1/p/coreclient/{client_id}/sweep`

Parameters|Description
----------|-----------
client_id| Required. This is the client ID as used by a 3rd party e.g. exchange
wallet_id| String. This is the originator wallet ID 
recipient_wallet_id| String This is the destination wallet ID
reference   | Reference for the transaction
expires | Expiration time for transaction if not approved by custodians.
send | Required *symbol* Asset symbol e.g. BTC  *amount* quantity of assets being sent


Returns a transaction ID is returned.

* EXAMPLE SWEEP REQUEST RESPONSE

```json
{
  "tx_id": "EXAMpl31Deae4e80bda3daa7c3b97add"
}
```

### View sweep status

* EXAMPLE VIEW SWEEP STATUS RESPONSE

```json
{
  "approver_count": 0,
  "expires": 0,
  "initiated_timestamp": 0,
  "reference": "string",
  "status": [
    {
      "action_id": "string",
      "status": "string",
      "timestamp": 0,
      "entity": "string"
    }
  ],
  "status_details": {
    "amount": 0,
    "asset": "string",
    "expires": 0,
    "fees": 0,
    "wallet_id": "string",
    "net_amount": 0,
    "recipient_wallet_id": "string",
    "reference": "string",
    "requested_by": "string"
  },
  "threshold_required": 0,
  "tx_id": "string",
  "tx_status": "string"
}
```

View the status of a Sweep by providing the **client_ID** and **tx_id**.

Use the following endpoint to create a new sweep.

`POST https://api.qredo.network/api/v1/p/coreclient/{client_id}/sweep/tx_id`

Parameters|Description
---|---
client_id| Required. This is the client ID as used by a 3rd party e.g. exchange
tx_id| Required. This is the tx_id returned when the sweep request was made

Returns the Sweep Transaction object.

