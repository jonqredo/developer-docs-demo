---
comment: wip does not yet support user to understand all config options
title: Configure Signing Agent
author: HB and Signing Agent team's docs
proofedDate: 20221217
---

## TL;DR

The [Docker image](docker) provided for Signing Agent references a control file, or configuration file, `config.yaml`. 

* Download the [config.yaml from GitHub](https://github.com/qredo/signing-agent/blob/master/config-template.yaml).

## Configuration options

The following settings are configurable via the YAML file:

- Base
- Auto approval
- Qredo WebSocket
- HTTP
- Logging
- Load balancing
- Store 

* [Learn more](#cloud-secret-storage) about setting up cloud storage of secrets.

## Configuration properties

Customize the properties of `config.yaml` to suit your needs.

The following table presents each property.

Property|Sub-property|Sub-property/Options|Description|
|--------|-----------|---------|-----------------------------|
|base|||Controls connection properties|
||qredoApi|**https://api.qredo.network/api/v1/p** or https://sandbox-api.qredo.network/api/v1/p |Base domain for the production or sandbox environment|
||pin||Pin number used to provide a Zero-Knowledge proof token for communication with the [Partner API](/developer-guides/partner-api). This token is passed in the “x-api-zkp” header for authentication.|
|autoApproval|||Enables automatic approval of all transactions|
||enabled|`true`, `false`|Toggles automatic approval on/off|
||retryIntervalMaxSec| |Maximum time for which the Signing Agent retries approval attempts. Failure fallback applies after this period|
||retryIntervalSec||Interval between approval attempts. Cycled until `retryIntervalMaxSec` is reached.|
|websocket|||This is returned with `agentID` when the agent is registered|
||qredoWebsocket||Qredo provides a WebSocket to collect transaction approval requests from|
||reconnectTimeoutSec| |Timeout in seconds for connection attempt|
||reconnectIntervalSec| |Interval in seconds between connection attempts|
||pingPeriodSec|{% comment %} no rate limit {% /comment %}|The ping period for the ping handler in seconds|
||pongWaitSec||The pong wait for the pong handler in seconds|
||writeWaitSec||The write wait in seconds|
||readBufferSize||The WebSocket upgrader read buffer size in bytes|
||writeBufferSize||The WebSocket upgrader write buffer size in bytes|
|http|||
||addr||The address and port the service runs on {% comment %} WDYM? the image builds and runs at this port? todo Abel {% /comment %}|
||CORSAllowOrigins|*|Optional: The value assigned to the Access-Control-Allow-Origin of the responses of the build in API. If not required, pass *. {% comment %} WDYM? todo Abel {% /comment %}|
||logAllRequests|`true`, `false`|Toggles logging of incoming requests to the build in API on/off{% comment %} WDYM? todo Abel {% /comment %}|
||TLS||TLS configurations for the internal HTTP server. Used to determine whether HTTP or HTTPS is applied. |
|||`enabled`|Toggles whether TLS is enabled on/off with `true`, `false`|
|||`keyFile`|If enabled is true, specifies the key file to use for the TLS server|
|||`certFile`|If enabled is true, specifies the cert file to use for the TLS server|
|logging|||
||format|`text`, `json`|File format for the log|
||level|`debug`, `info`, `warn`, `error`|Log level; default is debug|
|loadBalancing|
||enable|`true`, `false`|Toggles whether load balancing logic is on/off|
||onLockErrorTimeoutMs||On lock timeout in milliseconds|
||actionIDExpirationSec||Expiration of `action_id` variable in Redis in seconds|
||redis|||
|||host|Redis host|
|||port|Redis port|
|||password|Redis password|
|||db|Redis database name|
|store|||Store type holding private key information for the Signing Agent| 
||type|`file`, `oci`, `aws` ||
||file||If `store` type is `file`, pass path to the storage file|
||oci||If `store` type is Oracle cloud configuration, details for the Oracle vault|
|||compartment|Oracle Cloud Identifier (OCID) where the vault and encryption key reside|
|||vault|OCID of the vault where the secret will be stored|
|||secretEncryptionKey|Encryption key used for both the secret and the data inside the secret|
|||configSecret|Name of secret that will be used to store the data|
||aws||Amazon cloud configuration to store the private keys in amazon secrets manager|
|||region|If using AWS Secrets Manager, AWS region where the secret is stored|
|||configSecret|If using AWS Secrets Manager, name of the AWS Secrets Manager secret containing the encrypted data|

{% comment %} todo Abel are we telling user to store redis password in config file? Need warning about this if so.. {% /comment %}


### Example configuration values

{% callout type="warning" %}
Note, this is not an example configuration file, as examples are provided for properties that would conflict, such as storing/passing the private key by different methods.
{% /callout %}



Property|Sub-property|Sub-property|Example|
|--------|-----------|-----------|-----------------------------|
|base||||
||qredoApi||sandbox-api.qredo.network|
||pin|| 0|
|autoApproval||||
||enabled||false|
||retryIntervalMaxSec||300|
||retryIntervalSec||5|
|websocket||||
||qredoWebsocket||wss://sandbox-api.qredo.network/api/v1/p/coreclient/feed|
||reconnectTimeoutSec||300|
||reconnectIntervalSec||5|
||pingPeriodSec||5|
||pongWaitSec||10|
||writeWaitSec||10|
||readBufferSize||512|
||writeBufferSize||1024|
|http||||
||addr||0.0.0.0:8007|
||CORSAllowOrigins||*|
||logAllRequests||false|
|TLS||||
||`enabled`||`true`|
||`keyFile`||tls/domain.crt|
||`certFile`||tls/domain.key|
|logging||||
||format||text|
||level||debug|
|loadBalancing|
||enable||false|
||onLockErrorTimeoutMs||300|
||actionIDExpirationSec||6|
||redis|||
|||host|redis|
|||port|6379|
|||password|""|
|||db|0|
|store||| 
||type||file |
||file||/volume/ccstore.db|
||oci||
|||compartment|ocid1.tenancy.oc1...|
|||vault|ocid1.vault.oc1...|
|||secretEncryptionKey|ocid1.key.oc1...|
|||configSecret|automatedApproverConfig|
||aws|||
|||region|aws-region-...|
|||configSecret|secretsManagerSecret...| 


{% comment %} 
todo deeper dive needed for ## Understand your configuration options

## Example configuration file

{% /comment %}


## Cloud secret storage

An alternative to storing the Signing Agent configuration on-premises in a file is to use secure cloud-based storage. The following cloud-based solutions are supported.

### Oracle Cloud Vault Storage

In order to use Oracle Cloud Vault storage, update your configuration storage setting, i.e. set the `store` `type` to `oci` in the [YAML configuration file](https://developers.qredo.com/signing-agent/v2-signing-agent/configure/).

For example, your YAML config should look something like the following:

```yaml

store:
  oci:
    vault: ocid1.vault....
    secretEncryptionKey: ocid1.key....
    compartment: ocid1.tenancy....
    configSecret: signing_agent_config
  ...
```

- Set up an API key on Oracle Cloud
- Download the config file for the API key, fill in the correct private key path
- Either put the file in its default location of ~/.oci/config or
  put it in a custom location and set env var OCI_CONFIG_FILE to the full path including the filename
- Create a vault and copy the OCID to the config file for the vault setting
- Create an encryption key (AES or RSA) in the vault, copy it's OCID to the config secretEncryptionKey setting
- Copy the compartment OCID from the compartment where the vault was created
- Set a secret name where the Signing Agent will store its configuration and keys
- Start the Signing Agent and register an agent using the API

### AWS Cloud Secrets Manager Storage

In order to use AWS for configuration storage, update your configuration storage setting, i.e. set the `storage_type` to `aws` and provide the AWS Region and the name of the Secrets Manager secret in the YAML configuration file. 

For example, your YAML config should look something like the following:

```yaml
store:
  aws:
    region: eu-west-2
    configSecret: signingAgentConfig
  ...
```

The Secrets Manager secret (i.e. `signAgentConfig` in this example) needs to be set up in advance. To do this, on the AWS console:

1. Create a KMS customer-managed key to be used to encrypt the Secrets Manager secret
2. Create the Secrets Manager secret, naming it and including the KMS key from step 1
   - Select `Other type of secret`
   - Select `Plaintext` and enter `initialise me`
   - From the Encryption key drop-down, select the key created in step 1
   - Name the secret and optionally add a description
3. Update the Signing Agent's configuration file with the AWS region and secret name

Start the Signing Agent and register the agent using the API.

* Starting the Agent converts the secret's type from plaintext to binary.

**Note:** the Signing Agent needs access to AWS credentials in order to use AWS' Secrets Manager. How you do this is dependent on your requirements (for instance, the use of AWS access keys or an AWS credentials file, etc.) but, importantly, if running the Signing Agent in a Docker container, the AWS credentials need to be available to the Agent running in the container. This requires passing the AWS credential data to Docker at startup. As an example, AWS access keys can be passed as environment variables:

```shell
> docker run -e AWS_ACCESS_KEY=$AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY ...
```
or, if using an AWS credential data contained in a local directory, something like this:

```shell
> docker run -v $HOME/.aws:/root/.aws ...
```
Specifics are best discussed with your cloud services admin department.

## Config Gottchas

### HTTP/TLS

If you wish to configure the TLS settings, you must supply the `certFile` and `keyFile` to setup HTTPS.


