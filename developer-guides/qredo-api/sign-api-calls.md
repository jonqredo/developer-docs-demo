---
comment: I have updated the doc but NOT verified the instructions work for the tooling // applied API tool as name (not as proper noun)
title: Sign Qredo API calls
author: TK> HB
proofedDate: 20221122
---

This page demonstrates signing requests and encrypting them for transfer.

{% callout type="note" %}
The [Signature](#signatures) of the call contains the payload of your request which is encrypted using your *API secret*.
{% /callout %}

## Prerequisites

You have created your [API key and secret](generate-keys). 

## Authenticate requests

Pass your API key as a header to authenticate your API request. 

## Authorize requests

The Qredo API authorizes all requests by examining a *Signature* (passed as `qredo-api-sig` header) combined with a *timestamp* (passed as `qredo-api-ts` header).

## Signatures

Sign and encrypt the Signature of your API calls with your *API secret*.

The signature is the URL-safe Base64 encoding (RFC 4648) of the HMAC SHA256 hash of the following string: `[timestamp][method][full path url][body]`, signed using your *API secret*. 

Therefore, the payload you sign is a concatenated string of the following:

- an Epoch timestamp in nanoseconds
- an HTTP method
- the full URL
- a JSON body (optional)

### Construct your signature

To generate your signature hash:

1. Construct the request to sign in the format: ```[timestamp][method][URL][body]```.

    The timestamp used for signing must be the same one that is used in the header `qredo-api-ts`.
    
    For example, suppose I want to sign an API call to `GET /balance`. The parameters are as following:
    * timestamp: `1647356399`
    * HTTP method: `GET`
    * URL: `https://api.qredo.network/qapi/v1/balance`
    * body: _none_ (as it is a `GET` request)

    The resulting string that will need to be signed is: `1647356399GEThttps://api.qredo.network/qapi/v1/balance`

2. Using the decoded secret, sign the request as constructed in Step 1 with the HMAC-SHA256 algorithm. If you are copy-pasting the secret from the web app, you will need to base64-decode it first.
3. Encode the signed payload with URL-safe Base64 encoding.
4. Assign the signature to the `qredo-api-sig` header.

{% callout type="note" title="Send the body exactly as signed" %}
After signing a body, make sure to send the JSON body formatted precisely as signed. Raw, unsigned JSON will generate an error.
{% /callout %}

The following is a Python example showing how to generate the `qredo-api-sig` header:

```python
def signGET(xtime, xmethod, xurl):
    secret_decode = base64.b64decode(api_secret)
    xcat1 = str(xtime) + xmethod + xurl
    xcat2 = xcat1.encode("utf-8")
    xhash = hmac.new(key=secret_decode, msg=xcat2, digestmod=hashlib.sha256).digest()
    xsig1 = base64.urlsafe_b64encode(xhash).rstrip(b"=")
    xsig2 = str(xsig1).strip("b\'")
    return xsig2

def signJSON(xtime, xmethod, xurl, xbody):
    secret_decode = base64.b64decode(api_secret)
    xbody2 = json.dumps(xbody)
    xcat1 = str(xtime) + xmethod + xurl + xbody2
    xcat2 = xcat1.encode("utf-8")
    xhash = hmac.new(key=secret_decode, msg=xcat2, digestmod=hashlib.sha256).digest()
    xsig1 = base64.urlsafe_b64encode(xhash).rstrip(b"=")
    xsig2 = str(xsig1).strip("b\'")
    return xsig2
```

