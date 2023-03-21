---
comment:
title: Deploy Signing Agent
author: HB and Signing Agent team's docs
proofedDate: 20021217
---

## TL;DR

- Register Signing Agent with the Partner API via the self-serve API

{% callout type="note" %}
Ensure that you have everything you need to hand:

- Qredo account with supporting Partner API keys enabled
    - Access to active API key
    - Access to private key
- Customized Signing Agent [config.yaml](configure) file set up
- [Docker](docker) volume and container running

{% /callout %}

## Encode your API key

Generate a Base64 representation of your `private.pem` key:

* e.g. for a Mac: `base64 -i private.pem`

Copy your Base64-encoded token to the clipboard or a text file. You are going to use it as a part of the payload data on the registration endpoint when making the API call to the Signing Agent service.

{% callout type="note" %}
Never run the service directly connected to the internet. All external requests to the API endpoints exposed by the Signing Agent should be done through a reverse proxy service (e.g. Nginx or HAProxy) and over TLS.

{% /callout %}

## Verify your deployment

There are currently three healthcheck endpoints available that allow you to verify that your deployment is successful.

{% tabs %}
{% tab label="/healthcheck/version" %}
The [version healthcheck endpoint](/api-reference/signing-agent#tag/healthcheck/operation/HealthCheckVersion) expects a `GET` request, and it responds with an HTTP 200 status code and a JSON payload containing the current build information:

```json
{
    "buildVersion": "101c354",
    "buildType": "dev",
    "buildDate": "Fri Oct 7 12:53:12 UTC 2022"
}
```
{% /tab %}
{% tab label="/healthcheck/config" %}
The [config healthcheck endpoint](/api-reference/signing-agent#tag/healthcheck/operation/HealthcheckConfig) accepts a `GET` request, and it responds with an HTTP 200 status code and a JSON payload containing the current configuration file data:

```json
{
    "base": {
        "qredoAPI":"https://sandbox-api.qredo.network/api/v1/p",
        ...
    },
    "http": {
        "addr":"0.0.0.0:8007",
        "CORSAllowOrigins":["*"],
        ...
    },
    "autoApproval": {
        "enabled": false,
        ...
    },
    ...
}
```
{% /tab %}
{% tab label="/healthcheck/status" %}
The [status healthcheck endpoint](/api-reference/signing-agent#tag/healthcheck/operation/HealthcheckStatus) accepts a `GET` request, and it responds with an HTTP 200 status code and a JSON payload containing the status information for various services or external connections that are related to the Signing Agent service:

```json
{
    "websocket": {
        "readyState":"CLOSED",
        "remoteFeedURL": "wss://sandbox-api.qredo.network/api/v1/p/coreclient/feed",
        "localFeedURL":"ws://0.0.0.0:8007/api/v1/client/feed",
        "connectedClients": 2
    }
}
```
{% /tab %}
{% /tabs %}

## Register your Signing Agent

Note, the Docker volume provides a self-serve Rest API service that registers your Signing Agent with Partner API. [Call `/register`](/api-reference/signing-agent#tag/client/operation/RegisterAgent), substituting your URL for the Signing Agent service.


### Example request

{% callout type="note" %}
For the following example, we assume the Signing Agent service is running at `http://localhost:8007`. You should supply your address and port of the signing service you started with the [Docker image](docker).

```curl
curl -X 'POST' \
  'http://localhost:8007/api/v1/register' \
  -H 'Content-Type: application/json' \
  -d '{
        "name": "Test Agent",
        "APIKey": "Partner APIKey from the WebApp",
        "base64PrivateKey": "token from the clipboard"
}'
```
{% /callout %}

### Example response

```JSON
{
    "agentID":"EXAMpl31DbS7SgE21Ht5xRHFWRbcrH9AMZHgsoUd2gpp",
    "feedURL":"ws://localhost:8007/api/v1/client/feed"
}
```
{% callout type="note" %}
Retain your `agentID`, you will need this later.
{% /callout %}

