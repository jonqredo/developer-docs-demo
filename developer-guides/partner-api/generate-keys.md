---
comment:
title: Generate Partner API keys
author: TK> HB
proofedDate: 20021124
---

## Prerequisites

To generate keys, you must have: 

- a [Qredo account](https://qredo.zendesk.com/hc/en-us/articles/4404085365649-Creating-Your-Account)
- [API access enabled](/developer-guides/partner-api/get-started)

## Overview

1. Sign in to Qredo Web App and [generate API keys](#api-keys-for-authentication) for API user authentication.
2. [Generate RSA API sign keys](#rsa-keys-for-signing) to authorize and sign API requests.
3. In the Qredo Web App, [add the public RSA key](#upload-public-rsa-key-to-qredo-web-app) created.

{% callout type="warning" %}
You must **never** use API keys in the frontend of your application or malicious users could discover them in your source code. You should only use them on your server. You must never embed API keys in your backend code as this increases the risk of discovery.
{% /callout %}

## API keys for authentication

The Qredo API keys are Base64-encoded and are used for API user access. There are two types of Qredo authentication API keys:

* Production keys: use these for live action with the Partner API.
* Sandbox keys: use these to *test* your integration with the Partner API.

{% callout type="note" title="Sandbox vs. Production keys" %}
Sandbox and production keys are used in the same environment. There is no difference regarding their use with the API: the base URL and all endpoints are the same.
{% /callout %}

### Generate API keys in Qredo Web App

1. Sign into the Qredo Web App.

2. In the top right corner, click on your name, select the **Account** type you need, and click **Settings**.

    {% callout type="note" title="Further support" %}
    Switching accounts is described in greater detail [here](/user-guides/api-keys#select-the-account).
    {% /callout %}

3. From the tabs, select **Partner API** to display the following screen:

    ![partnerapi](/images/partner-api/api-access-keygen.png)

4. Click **Generate API Key**.

    ![copy-key](/images/partner-api/copy-api-key.png)

5. Click **Copy to clipboard**. This copies the Base64-encoded authentication key for the environment to your clipboard.

6. Paste the key into a text file and save it as `apikey` (no file extension). You will use this file with the [Partner API Signing Client](sign-api-calls#partnerapi-signing-client).

{% callout type="note" title="Regenerate keys if needed" %}
If you lose or forget to copy the API key, complete the procedure again to regenerate the API key.
{% /callout %}

{% callout type="warning" title="Keep your keys safe!" %}
* **DO NOT** share your keys with unauthorized parties.
* **DO NOT** store your keys in easily accessible or compromizable locations.
{% /callout %}


## RSA keys for signing

The API requires RSA key pairs for authorizing and signing requests.

* The Qredo server verifies the signature against the public RSA API key.
* The private RSA API key signs the requests.

### Generate RSA keys using the CLI

The following example uses the terminal CLI (Command Line Interface) for generating the RSA key pair, but you can use any preferred tool to perform this task. The Qredo Partner API works with 2048-bit RSA keys.

1. Generate a 2048-bit RSA key pair and store the private key in a file named `private.pem` using the CLI command:

    ```bash
    openssl genrsa -out private.pem 2048
    ```

    You should see the following message:

	```bash
	Generating RSA private key, 2048-bit long modulus...
	```

    {% callout type="note" %}
    Some versions of OpenSSL may require an additional argument. For OpenSSL 3.0.7 (20021101), also pass -`traditional`:
    ```bash
    openssl genrsa -traditional -out private.pem 2048
    ```
    {% /callout %}

           

2. Extract the public key from the key pair and store it in a file named `public.pem` using the command:

    ```bash
    openssl rsa -in private.pem -outform PEM -pubout -out public.pem
    ```

    You should see the following message:

    ```bash
    writing RSA key
    ```

The key pair files `private.pem` and `public.pem` are created in the current working folder.

* Private key: you will use to sign, encrypt, and authorize API calls. This is detailed in the next topic, [Sign API calls](sign-api-calls#partnerapi-signing-client).
* Public key: you will [add to your Qredo Wallet Web App account](#upload-public-rsa-key-to-qredo-web-app) after obtaining API access.


### Upload public RSA key to Qredo Web App

You need to associate the [RSA key pair](#generate-rsa-keys-using-the-cli) to your Qredo account by adding your public RSA keys using the Qredo Web App.

To complete this procedure, return to the Partner API page in the Web App:

1. Sign in to the Qredo Web App.

2. In the top right corner, click on your name and open **Settings** for the required account.
   
3. Select **Upload Public Key** to open a dialog box.

4. Locate the `public.pem` RSA key file you created earlier and open it using a text editor or list in your terminal.

    ```bash
    cat public.pem
    ````

5. Copy the entire contents and paste into the dialog box.

    ```bash
    -----BEGIN PUBLIC KEY-----
    ExAMplENBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyk14HdJt9Tc/Pws3aFLL
    9PAt4x+OnuLGmLQHWAM7vucuOR3+rlrpkHPC+9T17sxvVq/BFx2Rkd9pSqD130/W
    S0MVqCXOwYzB3YdTtiHNNBS4I3fUViYfz7hF3YGUtimMg1RQIerr15bIMUVdAdFY
    ze7MHYwgYb5YRMUa9d6k4Ehq9lPmwvHwEFVSTCAl9XvGU5+qUK+kLYim2gcdhgXQ
    Oe8M1bGmWvEOgxg5brUPHzquPMlShiRMO6mibCg6pSb7HsFAucoB8y3soDtHe5Zr
    qJHgsHTuNhX2PPdOgYVuX8KiTD28zY2VCkDux9DabJ7J5RdxS1nNszea2o9qWhf1
    +QIDAQAB
    -----END PUBLIC KEY-----
    ```

6. Click **Upload**.

7. Success: a green `LIVE` indicator appears.

    {% callout type="note" %}
    More recent versions of the Web App support just one key type. Use the key in Production and/or the Sandbox environments according to your need.
    {% /callout %}

    ![sandboxuploadconfirmation](/images/partner-api/sandbox-upload-key-confirmation.png)

Next, understand how to [authenticate and authorize](sign-api-calls) API calls.