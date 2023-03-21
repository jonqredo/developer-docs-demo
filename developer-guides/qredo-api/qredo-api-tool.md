---
comment: duplicating testnet asset tip into signing-agent
title: Qredo API tool
author: TK > HB
proofedDate: 20221122
---

Qredo provides a tool to assist with signing your signature and testing your integration.
This page describes the testing tool. We also recommend you use the [Sandbox environment](#sandbox) to perform your tests.

The [Qredo API tool](https://github.com/qredo/apitool):

- Signs API requests
- Submits signed API requests to Qredo
- Is available both as a command-line tool and as a Web UI

This Golang tool is hosted on [GitHub](https://github.com/qredo/apitool). You are welcome to clone or fork the repository to adapt it to your needs when building your custom client for signing and sending requests.

{% comment %} wip Why is this here and not in the Readme of that repo? {% /comment %}


{% callout type="warning" title="The API tool is for tests only" %}
Qredo provides the API tool for illustrative purposes. We don't recommend using it in production.
{% /callout %}

## Prerequisites

- You have created your API [key and secret](generate-keys). 
- You are comfortable sending headers: 
    - Signature:`qredo-api-sig`
    - Timestamp: `qredo-api-ts`
    - API key: `qredo-api-key`

{% callout type="note" title="Enable testnet assets" %}
While not mandatory, Qredo strongly recommends you perform API tests using [testnet assets](/user-guides/testnet).
{% /callout %}

## Install Golang

To build and run the Signing Client, you must have [Golang](https://go.dev/) installed. Using a command-line tool, verify whether you have Golang installed:

```go
go version
```

If you don't have Golang, install a recent stable version to proceed.

## Build the API tool

To build the API tool, take the following steps:

1. Clone a local copy of the [API tool](https://github.com/qredo/apitool).

2. Navigate to the local `apitool` repository.

3. Using the command-line tool, build the API tool to work with your keys:

{% tabs %}
{% tab label="Linux/Mac" %}
```go
go build -o apitool
```
(You can also run this command on Windows using PowerShell / Git Bash.)
{% /tab %}
{% tab label="Windows" %}
Use command prompt: `cmd.exe`:
```go
go build -o apitool.exe
```
{% /tab %}
{% /tabs %}

The command-line tool prints out a message about the apitool executable location. You can now proceed with using the API tool.

## Sign API requests in the command line

In the command-line tool, run the following command:

{% tabs %}
{% tab label="Linux/Mac" %}
```
./apitool -api-key 9OgjbzwhoE4LJA \
-secret EXAMpleAPIsecrETthatCONTAINSnumber5andLetT3r5y8vdERqOFZCNHlzaWZWYjBzOG9BcWU4a0Uu \
-method GET \
-url https://api.qredo.network/qapi/v1/balance sign
```
(You can also run this command on Windows using PowerShell / Git Bash.)

The command-line tool prints out the Signature (`qredo-api-sign` header) and the timestamp (`qredo-api-ts` header):
```
qredo-api-sign: S1GnAxSlgbL0-KA47kBwa0c7XLgX7cWUi00NMXYV5jA
qredo-api-key: KeY9OgjhoE4LJA
qredo-api-ts: 1234438269
```
{% /tab %}
{% tab label="Windows" %}
Use command prompt: `cmd.exe`:
```
apitool -api-key k3y9OgjhoE4LJA -secret EXAMpleAPIsecrETthatCONTAINSnumber5andLetT3r5y8vdERqOFZCNHlzaWZWYjBzOG9BcWU4a0Uu8vdERqOFZCNHlzaWZWYjBzOG9BcWU4a0Uu -method GET -url https://api.qredo.network/qapi/v1/balance sign
```
The command-line tool prints out the Signature (`qredo-api-sign` header) and the timestamp (`qredo-api-ts` header):
```
qredo-api-sign: S1GnAxSlgbL0-KA47kBwa0c7XLgX7cWUi00NMXYV5jA
qredo-api-key: KeY9OgjhoE4LJA
qredo-api-ts: 1234438269
```
{% /tab %}
{% /tabs %}

You can now proceed to send a request.

## Send API requests in the command line

In the command-line tool, run the following command:

{% tabs %}
{% tab label="Linux/Mac" %}
```
./apitool -api-key k3y9OgjhoE4LJA \
-secret EXAMpleAPIsecrETthatCONTAINSnumber5andLetT3r5y8vdERqOFZCNHlzaWZWYjBzOG9BcWU4a0Uu8vdERqOFZCNHlzaWZWYjBzOG9BcWU4a0Uu \
-method GET \
-url https://api.qredo.network/qapi/v1/balance send
```
(You can also run this command on Windows using PowerShell / Git Bash.)

The command-line tool prints out the API call response as uncompressed JSON:
```
{"assets":{"ETH-TESTNET":{"total":1800000000,"available":1800000000,"pendingIn":0,"pendingOut":0,"scale":1000000000}}}

```
{% /tab %}
{% tab label="Windows" %}
Use command prompt: `cmd.exe`:
```
apitool -api-key KeY9OgjhoE4LJA -secret EXAMpleAPIsecrETthatCONTAINSnumber5andLetT3r5y8vdERqOFZCNHlzaWZWYjBzOG9BcWU4a0Uu8vdERqOFZCNHlzaWZWYjBzOG9BcWU4a0Uu -method GET -url https://api.qredo.network/qapi/v1/balance send
```
The command-line tool prints out the API call response as uncompressed JSON:
```
{"assets":{"ETH-TESTNET":{"total":1800000000,"available":1800000000,"pendingIn":0,"pendingOut":0,"scale":1000000000}}}
```
{% /tab %}
{% /tabs %}


## Sign and send requests via Web UI

The API tool initiates a locally-served Web UI providing simple controls to sign and send API calls.

### Initiate the Web UI API tool

{% tabs %}
{% tab label="Linux/Mac" %}
```
./apitool ui     
```
(You can also run this command on Windows using PowerShell / Git Bash.)

The command-line tool prints out the local host location of the API tool Web UI:
```
WebUI listening on http://127.0.0.1:4569
```
{% /tab %}
{% tab label="Windows" %}
Use command prompt: `cmd.exe`:
```
apitool ui
```
The command-line tool prints out the local host location of the API tool Web UI:
```
WebUI listening on http://127.0.0.1:4569
```
{% /tab %}
{% /tabs %}


### Sign and send requests

The Web UI supports:

* Signing a request: Click **Sign**.

  The application returns the API call Signature and timestamp.

* Signing and sending a request: Click **Send**.
    
    The application returns the API call Signature and timestamp plus the HTTP response body.

With the Web UI running, open the local host location [http://127.0.0.1:4569](http://127.0.0.1:4569) in a browser and follow these steps:

{% tabs %}
{% tab label="Sign and Send requests" %}
1. Enter your [*API key* and *API secret*](generate-keys).
2. Enter the full *URL* path of your selected API endpoint. For example, with `GET /balance`, enter `https://api.qredo.network/qapi/v1/balance`.
3. Select the HTTP method of the API request you want to send. For example, with `GET /balance`, set this value to `GET`.
4. In the **Body** text box, enter the API call body. Applicable to requests with HTTP methods such as `POST` or `PUT`. Leave blank when the request doesn't have a body, e.g., `GET` requests.
5. Click **Send**. The API tool Web UI prints out the results in two sections of the screen:
        * To the right of the **Body** text box, you can observe your *API key* shown as (`Qredo-API-Key`), the *API call timestamp* (`Qredo-API-Ts`), and the *API call Signature* (`Qredo-API-Sig`).
        * Below the buttons **Send** and **Sign**, the web UI API tool prints out the response JSON body.
{% /tab %}
{% tab label="Sign requests" %}
1. Enter your [*API key* and *API secret*](generate-keys).
2. Enter the full *URL* path of your selected API endpoint. For example, with `GET /balance`, enter `https://api.qredo.network/qapi/v1/balance`.
3. Select the HTTP method of the API request you want to send. For example, with `GET /balance`, set this value to `GET`.
4. In the **Body** text box, enter the API call body.  
5. Click **Sign**. The API tool Web UI prints out the results to the right of the screen including: your `Qredo-API-Key`, the `Qredo-API-Ts` timestamp, and the *API call Signature*, shown as the value for **Qredo-API-Sig**.
{% /tab %}
{% /tabs %}


