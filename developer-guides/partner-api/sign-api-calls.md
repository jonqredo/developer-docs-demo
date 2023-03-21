---
comment: WARNING -- Test signing requests uses the Swagger Ui for mocking because there is currently no mocking supported from this site. This page should be deprecated in favour of a solution run from this site. Once done -- update the link shown in "Test signing requests" section below.
title: Sign Partner API calls
author: TK
proofedDate: 
---

## TL;DR

With every API request header you must:

- Authenticate: by passing the [API key](generate-keys)
- Authorize: by passing an encoded [signature](#signatures-in-partner-api) generated using your RSA key

{% callout type="note" title="Optional" %}
Apply the dev toolkit [Signing Client](#partner-api-signing-client)
{% /callout %}


## Overview

In this section, you will learn how to:

* Deploy the Partner API Signing Client
* Start signing requests using the Partner API Signing Client

## Authorize

The Qredo Partner API authorizes all requests using a *signature* (passed as a `x-sign` header), combined with a *timestamp* or *nonce*.

To facilitate your experience with signing API requests, Qredo provides the [Partner API Signing Client](https://github.com/qredo/partner-api-sign): an example client application that generates signatures and timestamps with API calls.


### Signatures in Partner API

The signature is the URL-safe Base64 encoding (RFC 4648) of the RSA PKCS #1 1.5 signature of the SHA256 hash of the payload to be signed.

The payload to be signed is the nonce or timestamp concatenated with the URL of the request and the body in compressed JSON format (if that particular call requires a body).

{% callout type="note" title="Use either timestamp or nonce in Partner API calls" %}
Pass either `x-timestamp` or `x-nonce` header with **every** API call.

```curl
    -H "accept: application/json" \
    -H "x-timestamp: <Epoch_timestamp>" \
    -H "x-sign: <your_generated_signature>" \
    -H "x-api-key: <your_API_key>" \
```


```curl
    -H "accept: application/json" \
    -H "x-nonce: <Unique identifier>" \
    -H "x-sign: <your_generated_signature>" \
    -H "x-api-key: <your_API_key>" \ 
```
{% /callout %}

Examples in this guide use timestamps only.

### Authorization steps

1. Construct the JSON body.
2. Construct the request to sign in the format: `[timestamp/nonce][URL][body]`

#### Sign `POST /company` using a timestamp

This example uses the `POST /company` API call: create a company.

{% tabs %}
{% tab label="Uncompressed JSON" %}
```
https://api.qredo.network/api/v1/p/company{
"name": "ACME Corp",
"city": "Paris",
"country": "FR",
"domain": "acme.com",
"ref": "9827feec-4eae-4e80-bda3-daa7c3b97add"
}
```
{% /tab %}
{% tab label="Compressed JSON" %}
 ```
https://api.qredo.network/api/v1/p/company{"name":"ACME Corp","city":"Paris","country":"FR","domain":"acme.com","ref":"9827feec-4eae-4e80-bda3-daa7c3b97add"}
```
{% /tab %}
{% /tabs %}

##### Construct the signature

1. Hash the private.pem with the SHA256 algorithm.

2. Sign the hash with RSA PKCS #1 1.5.

3. Encode the signature with URL-safe Base64 encoding.

{% comment %}
    ```bash
    base64 -i private.pem
    ```
This provides a encoded private key.
{% /comment %}

{% callout type="note" title="Send the body exactly as signed" %}
 After signing a `PUT` or `POST` request, make sure to send the JSON body formatted exactly as signed; otherwise, you will get an error.
{% /callout %}

## Partner API Signing Client

To assist with testing your integration, Qredo supplies the [Partner API Signing Client](https://github.com/qredo/partner-api-sign).

This tool, written in Golang, allows you to:

* Sign API requests
* Submit signed API requests to Qredo
* Listen on a WebSocket URL for incoming requests to fetch:
    * transaction and withdrawal requests [Core Client custody](/developer-guides/signing-agent/v1/set-up-core-client)
    * requests to connect either dedicated or Multi-Counterparty Wallets to an [exchange](/developer-guides/partner-api/exchanges)
    * [sweep transactions](/developer-guides/partner-api/exchanges)
    * [atomic swap quotes](/developer-guides/partner-api/atomic-swaps) posted to the liquidity hub

The Signing Client source code is written in Go and is available on GitHub. You can clone or fork the [repo](https://github.com/qredo/partner-api-sign) and use its code as an example when building your custom request-signing client.

{% callout type="warning" title="The Signing Client is for tests only" %}
The Partner API Signing Client is provided for illustrative purposes only. Qredo doesn't recommend its use for operations on Production.
{% /callout %}

### Install the Signing Client

To build and run the Signing Client, you must have [Golang](https://go.dev/doc/install) installed. Using a CLI, check if you have Golang installed by verifying the version:

```go
go version
```

If you don't have Golang, install a recent stable version to proceed.

1. Clone locally the [Partner API Signing Client](https://github.com/qredo/partner-api-sign) repo.
2. Copy or move the [`apikey`](generate-keys#generate-api-keys-in-qredo-web-application) file to the local `/partnerapi-sign` directory.
3. Copy or move your [`private.pem`](generate-keys#example-generate-rsa-keys-using-the-cli) file to the `/partnerapi-sign` directory. Remember that [you already added](generate-keys#generate-api-keys-in-qredo-web-application) the public key to your account in the Qredo web application.
4. Navigate to the local `partnerapi-sign` repository.
5. Using the CLI, build the Partner API Signing Client to work with your keys:

    #### Build Signing Client in CLI

    {% tabs %}
    {% tab label="Linux/Mac" %}
    You can also use this command on Windows using PowerShell:
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

    The cursor moves to a new line in the CLI, and you can proceed to sign requests.

### Sign requests with Signing Client

1. Run the following command:

    #### Run Signing Client in CLI

    {% tabs %}
    {% tab label="Linux/Mac" %}
    You can also use this command on Windows using PowerShell:
    ```
    ./partnerapi-sign sign
    ```
    {% /tab %}
    {% tab label="Windows" %}
    Use command prompt: `cmd.exe`:
    ```
    partnerapi-sign.exe sign
    ```
    {% /tab %}
    {% /tabs %}

2. In the prompt, enter the resource `url` (do not add the HTTP method, just the URL) and press `<Enter>` on your keyboard.

3. If you are sending a `POST` or `PUT` request, pass the JSON body to the next prompt.
    

    #### Sign API requests using Signing Client

    {% tabs %}
    {% tab label="Request without a body" %}
    This example uses `GET /company/search` to retrieve all companies per account:

    ```bash
    » ./partnerapi-sign sign
    url: https://api.qredo.network/api/v1/p/company/search
    body (hit Ctrl+Z followed by <enter> to end):
    ```

    There is no JSON body to add: on your keyboard, press 'Ctrl+D' twice (or 'Ctrl+Z' if you're using Windows) and then `Enter`.
    {% /tab %}
    {% tab label="Request with a JSON body" %}
    This example uses `POST /company` to create a new company:

    ```bash
    » ./partnerapi-sign sign
    url: https://api.qredo.network/api/v1/p/company
    body:
    {
        "name": "Randomcompanyname",
        "city": "Paris",
        "country": "FR",
        "domain": "randomcompanyname.com",
        "ref": "00-00-0a1"
    }
    ```

    If there is a JSON body, copy-paste it in response to the `body` prompt. Next, on your keyboard, press 'Ctrl+D' (or 'Ctrl+Z' if you're using Windows) and then `Enter`.
    {% /tab %}
    {% /tabs %}

    With either request type (with or without a JSON body), the Signing Client returns a signature, and a timestamp.

    ```
    x-sign: Vc_KZBmtqdrFiGVPvDcSfmly-dK652y9kLvxSYo93hmqza_RZq1YtJXamsMAxCQTZf6JXviVLDaqtQIwjB1Jjl3ms6jyjDFC4wp0qNh9m43rNj4xY7tfoE98qzkmeTJ9BIXEvEkQtruRHsw0bG9cjQlNKmA2NtNEyquEkUsSBX1phFudvDDtIlUWqA
    x-timestamp: 1634226826
    ```

### Test signing requests

If you have completed all steps as described above, you can perform a final test: submit the `GET /company/search` request you signed just above.

1. Go to the [Swagger UI](https://play-swagger.qredo.network) and open the `GET /company/search` resource tab.
2. Select **Try it out** to enable editing the API call: the header parameters become available for input.
3. Copy each of the respective values of `x-timestamp` and `x-sign` and paste them in the respective fields in the Swagger UI.
    
    #### With `POST` requests, submit the JSON body as signed
    If you decide to test signing the `POST /company` resource described above (instead of `GET /company/search` ), don't forget to submit the JSON body formatted exactly as when signed.

4. Click **Execute**. You should get a `HTTP 200 OK` response with details about the company:
    
    ```json
    {
      "total_count": 2,
      "matches": [
        {
          "company_id": "1zVKz...ebFAP",
          "name": "Company One",
          "domain": "company-001.com"
        },
        {
          "company_id": "1zXMf...U5oU5",
          "name": "Company 2",
          "domain": "company-002.com"
        }
      ]
    }            
    ```
{% callout type="note" title="You are now ready to start using the API" %}
If you have completed all the steps, you can now begin using the Partner API.
{% /callout %}

## Signing Client Gotchas

* Testing the Signing Client is easier when you use an editor to compose, format, and edit your API requests and copy-paste them into the CLI.

* On the step when you sign a body, after you are finished (regardless if you need to paste a body or not), make sure you do not accidentally press any other key besides 'Ctrl+D' (or 'Ctrl+Z' if you're using Windows) followed by 'Enter'. Otherwise, the signing process might be affected and submitting the API call could result in the error code `HTTP 401` (bad signature).

* With API requests that have a JSON body (e.g. every POST request), you can use uncompressed OR compressed JSON. The only rule is: if you signed compressed, then submit compressed; if you signed uncompressed, then submit exactly the same formatted JSON as the one that was signed. Otherwise, you'll get error code `HTTP 401` (bad signature).
    ```json
    {
      "code": 401,
      "msg": "errPartnerAuth",
      "detail": {
        "reason": "signature"
      }
    }
    ``` 

* If you use a tool that formats JSON and adds *tabs* (as opposed to *space intervals*), the signature will not work, and the API call will result in error code `HTTP 401` (bad signature).

## Sandbox testing

To simplify testing, the sandbox environment does not require the same level of encryption to authenticate.

For example, the following curl command will create a company:

```bash
curl -X "POST" \ 
"https://sandbox-api.qredo.network/api/v1/p/company" \ 
-H "accept: application/json" \ -H 'X-API-KEY: '$APIKEY'' \ 
-H "Content-Type: application/json" \ 
-d '{ "name": "Test2Company", "city": "Paris", "country": "FR", "domain": "company.com", "ref": "a-b-c-d-e" }'
```

Where `$APIKEY` is the key supplied in the Web App.
