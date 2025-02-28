<h2 align="center">Authentication for Requests</h2>

<p align="center">
<a href="https://pypi.org/project/requests-auth/"><img alt="pypi version" src="https://img.shields.io/pypi/v/requests_auth"></a>
<a href="https://travis-ci.org/Colin-b/requests_auth"><img alt="Build status" src="https://api.travis-ci.org/Colin-b/requests_auth.svg?branch=develop"></a>
<a href="https://travis-ci.org/Colin-b/requests_auth"><img alt="Coverage" src="https://img.shields.io/badge/coverage-100%25-brightgreen"></a>
<a href="https://github.com/psf/black"><img alt="Code style: black" src="https://img.shields.io/badge/code%20style-black-000000.svg"></a>
<a href="https://travis-ci.org/Colin-b/requests_auth"><img alt="Number of tests" src="https://img.shields.io/badge/tests-183 passed-blue"></a>
<a href="https://pypi.org/project/requests-auth/"><img alt="Number of downloads" src="https://img.shields.io/pypi/dm/requests_auth"></a>
</p>

Provides authentication classes to be used with [`requests`][1] [authentication parameter][2].

<p align="center">
    <a href="https://oauth.net/2/"><img alt="OAuth2" src="https://oauth.net/images/oauth-2-sm.png"></a>
    <a href="https://www.okta.com"><img alt="OKTA" src="https://www.okta.com/sites/all/themes/Okta/images/logos/developer/Dev_Logo-03_Large.png" height="120"></a>
    <a href="https://azure.microsoft.com/en-us/services/active-directory/"><img alt="Azure Active Directory (AD)" src="https://azurecomcdn.azureedge.net/cvt-cda59ccd0aa5ced6ff5a2052417cf596b92980921e88e667127eaca2232a31ab/images/shared/services/pricing-glyph-lock.svg" height="120"></a>
</p>
<p align="center">Some of the supported authentication</p>

## Available authentication

- [OAuth2](#oauth-2)
  - [Authorization Code Flow](#authorization-code-flow)
    - [OKTA](#okta-oauth2-authorization-code)
  - [Authorization Code Flow with PKCE](#authorization-code-flow-with-proof-key-for-code-exchange)
    - [OKTA](#okta-oauth2-proof-key-for-code-exchange)
  - [Resource Owner Password Credentials flow](#resource-owner-password-credentials-flow)
  - [Client Credentials Flow](#client-credentials-flow)
    - [OKTA](#okta-oauth2-client-credentials)
  - [Implicit Flow](#implicit-flow)
    - [Azure AD (Access Token)](#microsoft---azure-active-directory-oauth2-access-token)
    - [Azure AD (ID token)](#microsoft---azure-active-directory-openid-connect-id-token)
    - [OKTA (Access Token)](#okta-oauth2-implicit-access-token)
    - [OKTA (ID token)](#okta-openid-connect-implicit-id-token)
  - [Managing token cache](#managing-token-cache)
- API key
  - [In header](#api-key-in-header)
  - [In query](#api-key-in-query)
- [Basic](#basic)
- [NTLM (Windows)](#ntlm)
- [Multiple authentication at once](#multiple-authentication-at-once)

## OAuth 2

Most of [OAuth2](https://oauth.net/2/) flows are supported.

If the one you are looking for is not yet supported, feel free to [ask for its implementation](https://github.com/Colin-b/requests_auth/issues/new).

### Authorization Code flow

Authorization Code Grant is implemented following [rfc6749](https://tools.ietf.org/html/rfc6749#section-4.1).

Use `requests_auth.OAuth2AuthorizationCode` to configure this kind of authentication.

```python
import requests
from requests_auth import OAuth2AuthorizationCode

requests.get('http://www.example.com', auth=OAuth2AuthorizationCode('https://www.authorization.url', 'https://www.token.url'))
```

#### Parameters

| Name                    | Description                | Mandatory | Default value |
|:------------------------|:---------------------------|:----------|:--------------|
| `authorization_url`     | OAuth 2 authorization URL. | Mandatory |               |
| `token_url`             | OAuth 2 token URL.         | Mandatory |               |
| `redirect_uri_endpoint` | Custom endpoint that will be used as redirect_uri the following way: http://localhost:<redirect_uri_port>/<redirect_uri_endpoint>. | Optional | ''             |
| `redirect_uri_port`     | The port on which the server listening for the OAuth 2 code will be started. | Optional | 5000 |
| `timeout`               | Maximum amount of seconds to wait for a code or a token to be received once requested. | Optional | 60 |
| `success_display_time`  | In case a code is successfully received, this is the maximum amount of milliseconds the success page will be displayed in your browser. | Optional | 1 |
| `failure_display_time`  | In case received code is not valid, this is the maximum amount of milliseconds the failure page will be displayed in your browser. | Optional | 5000 |
| `header_name`           | Name of the header field used to send token. | Optional | Authorization |
| `header_value`          | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |
| `response_type`         | Value of the response_type query parameter if not already provided in authorization URL. | Optional | code |
| `token_field_name`      | Field name containing the token. | Optional | access_token |
| `code_field_name`       | Field name containing the code. | Optional | code |
| `username`              | User name in case basic authentication should be used to retrieve token. | Optional |  |
| `password`              | User password in case basic authentication should be used to retrieve token. | Optional |  |

Any other parameter will be put as query parameter in the authorization URL and as body parameters in the token URL.        

Usual extra parameters are:
        
| Name            | Description                                                          |
|:----------------|:---------------------------------------------------------------------|
| `client_id`     | Corresponding to your Application ID (in Microsoft Azure app portal) |
| `client_secret` | If client is not authenticated with the authorization server         |
| `nonce`         | Refer to [OpenID ID Token specifications][3] for more details        |

#### Common providers

Most of [OAuth2](https://oauth.net/2/) Authorization Code Grant providers are supported.

If the one you are looking for is not yet supported, feel free to [ask for its implementation](https://github.com/Colin-b/requests_auth/issues/new).

##### OKTA (OAuth2 Authorization Code)

[OKTA Authorization Code Grant](https://developer.okta.com/docs/guides/implement-auth-code/overview/) providing access tokens is supported.

Use `requests_auth.OktaAuthorizationCode` to configure this kind of authentication.

```python
import requests
from requests_auth import OktaAuthorizationCode


okta = OktaAuthorizationCode(instance='testserver.okta-emea.com', client_id='54239d18-c68c-4c47-8bdd-ce71ea1d50cd')
requests.get('http://www.example.com', auth=okta)
```

###### Parameters

| Name                    | Description                | Mandatory | Default value |
|:------------------------|:---------------------------|:----------|:--------------|
| `instance`              | OKTA instance (like "testserver.okta-emea.com"). | Mandatory |               |
| `client_id`             | OKTA Application Identifier (formatted as an Universal Unique Identifier). | Mandatory |               |
| `response_type`         | Value of the response_type query parameter if not already provided in authorization URL. | Optional | token |
| `token_field_name`      | Field name containing the token. | Optional | access_token |
| `nonce`                 | Refer to [OpenID ID Token specifications][3] for more details. | Optional | Newly generated Universal Unique Identifier. |
| `scope`                 | Scope parameter sent in query. Can also be a list of scopes. | Optional | openid |
| `authorization_server`  | OKTA authorization server. | Optional | 'default' |
| `redirect_uri_endpoint` | Custom endpoint that will be used as redirect_uri the following way: http://localhost:<redirect_uri_port>/<redirect_uri_endpoint>. | Optional | ''             |
| `redirect_uri_port`     | The port on which the server listening for the OAuth 2 token will be started. | Optional | 5000 |
| `timeout`               | Maximum amount of seconds to wait for a token to be received once requested. | Optional | 60 |
| `success_display_time`  | In case a token is successfully received, this is the maximum amount of milliseconds the success page will be displayed in your browser. | Optional | 1 |
| `failure_display_time`  | In case received token is not valid, this is the maximum amount of milliseconds the failure page will be displayed in your browser. | Optional | 5000 |
| `header_name`           | Name of the header field used to send token. | Optional | Authorization |
| `header_value`          | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |

Any other parameter will be put as query parameter in the authorization URL.        

Usual extra parameters are:
        
| Name            | Description                                                          |
|:----------------|:---------------------------------------------------------------------|
| `prompt`        | none to avoid prompting the user if a session is already opened.     |

### Authorization Code Flow with Proof Key for Code Exchange

Proof Key for Code Exchange is implemented following [rfc7636](https://tools.ietf.org/html/rfc7636).

Use `requests_auth.OAuth2AuthorizationCodePKCE` to configure this kind of authentication.

```python
import requests
from requests_auth import OAuth2AuthorizationCodePKCE

requests.get('http://www.example.com', auth=OAuth2AuthorizationCodePKCE('https://www.authorization.url', 'https://www.token.url'))
```

#### Parameters 

| Name                    | Description                | Mandatory | Default value |
|:------------------------|:---------------------------|:----------|:--------------|
| `authorization_url`     | OAuth 2 authorization URL. | Mandatory |               |
| `token_url`             | OAuth 2 token URL.         | Mandatory |               |
| `redirect_uri_endpoint` | Custom endpoint that will be used as redirect_uri the following way: http://localhost:<redirect_uri_port>/<redirect_uri_endpoint>. | Optional | ''             |
| `redirect_uri_port`     | The port on which the server listening for the OAuth 2 code will be started. | Optional | 5000 |
| `timeout`               | Maximum amount of seconds to wait for a code or a token to be received once requested. | Optional | 60 |
| `success_display_time`  | In case a code is successfully received, this is the maximum amount of milliseconds the success page will be displayed in your browser. | Optional | 1 |
| `failure_display_time`  | In case received code is not valid, this is the maximum amount of milliseconds the failure page will be displayed in your browser. | Optional | 5000 |
| `header_name`           | Name of the header field used to send token. | Optional | Authorization |
| `header_value`          | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |
| `response_type`         | Value of the response_type query parameter if not already provided in authorization URL. | Optional | code |
| `token_field_name`      | Field name containing the token. | Optional | access_token |
| `code_field_name`       | Field name containing the code. | Optional | code |

Any other parameter will be put as query parameter in the authorization URL and as body parameters in the token URL.        

Usual extra parameters are:
        
| Name            | Description                                                          |
|:----------------|:---------------------------------------------------------------------|
| `client_id`     | Corresponding to your Application ID (in Microsoft Azure app portal) |
| `client_secret` | If client is not authenticated with the authorization server         |
| `nonce`         | Refer to [OpenID ID Token specifications][3] for more details        |

#### Common providers

Most of [OAuth2](https://oauth.net/2/) Proof Key for Code Exchange providers are supported.

If the one you are looking for is not yet supported, feel free to [ask for its implementation](https://github.com/Colin-b/requests_auth/issues/new).

##### OKTA (OAuth2 Proof Key for Code Exchange)

[OKTA Proof Key for Code Exchange](https://developer.okta.com/docs/guides/implement-auth-code-pkce/overview/) providing access tokens is supported.

Use `requests_auth.OktaAuthorizationCodePKCE` to configure this kind of authentication.

```python
import requests
from requests_auth import OktaAuthorizationCodePKCE


okta = OktaAuthorizationCodePKCE(instance='testserver.okta-emea.com', client_id='54239d18-c68c-4c47-8bdd-ce71ea1d50cd')
requests.get('http://www.example.com', auth=okta)
```

###### Parameters

| Name                    | Description                | Mandatory | Default value |
|:------------------------|:---------------------------|:----------|:--------------|
| `instance`              | OKTA instance (like "testserver.okta-emea.com"). | Mandatory |               |
| `client_id`             | OKTA Application Identifier (formatted as an Universal Unique Identifier). | Mandatory |               |
| `response_type`         | Value of the response_type query parameter if not already provided in authorization URL. | Optional | code |
| `token_field_name`      | Field name containing the token. | Optional | access_token |
| `code_field_name`      | Field name containing the code. | Optional | code |
| `nonce`                 | Refer to [OpenID ID Token specifications][3] for more details. | Optional | Newly generated Universal Unique Identifier. |
| `scope`                 | Scope parameter sent in query. Can also be a list of scopes. | Optional | openid |
| `authorization_server`  | OKTA authorization server. | Optional | 'default' |
| `redirect_uri_endpoint` | Custom endpoint that will be used as redirect_uri the following way: http://localhost:<redirect_uri_port>/<redirect_uri_endpoint>. | Optional | ''             |
| `redirect_uri_port`     | The port on which the server listening for the OAuth 2 token will be started. | Optional | 5000 |
| `timeout`               | Maximum amount of seconds to wait for a token to be received once requested. | Optional | 60 |
| `success_display_time`  | In case a token is successfully received, this is the maximum amount of milliseconds the success page will be displayed in your browser. | Optional | 1 |
| `failure_display_time`  | In case received token is not valid, this is the maximum amount of milliseconds the failure page will be displayed in your browser. | Optional | 5000 |
| `header_name`           | Name of the header field used to send token. | Optional | Authorization |
| `header_value`          | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |

Any other parameter will be put as query parameter in the authorization URL and as body parameters in the token URL.        

Usual extra parameters are:
        
| Name            | Description                                                          |
|:----------------|:---------------------------------------------------------------------|
| `client_secret`        | If client is not authenticated with the authorization server     |
| `nonce`        | Refer to http://openid.net/specs/openid-connect-core-1_0.html#IDToken for more details     |

### Resource Owner Password Credentials flow 

Resource Owner Password Credentials Grant is implemented following [rfc6749](https://tools.ietf.org/html/rfc6749#section-4.3).

Use `requests_auth.OAuth2ResourceOwnerPasswordCredentials` to configure this kind of authentication.

```python
import requests
from requests_auth import OAuth2ResourceOwnerPasswordCredentials

requests.get('http://www.example.com', auth=OAuth2ResourceOwnerPasswordCredentials('https://www.token.url', 'user name', 'user password'))
```

#### Parameters

| Name               | Description                                  | Mandatory | Default value |
|:-------------------|:---------------------------------------------|:----------|:--------------|
| `token_url`        | OAuth 2 token URL.                           | Mandatory |               |
| `username`         | Resource owner user name.                    | Mandatory |               |
| `password`         | Resource owner password.                     | Mandatory |               |
| `timeout`          | Maximum amount of seconds to wait for a token to be received once requested. | Optional | 60            |
| `header_name`      | Name of the header field used to send token. | Optional  | Authorization |
| `header_value`     | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |
| `scope`            | Scope parameter sent to token URL as body. Can also be a list of scopes. | Optional |  |
| `token_field_name` | Field name containing the token.             | Optional  | access_token  |

Any other parameter will be put as body parameter in the token URL.

### Client Credentials flow

Client Credentials Grant is implemented following [rfc6749](https://tools.ietf.org/html/rfc6749#section-4.4).

Use `requests_auth.OAuth2ClientCredentials` to configure this kind of authentication.

```python
import requests
from requests_auth import OAuth2ClientCredentials

requests.get('http://www.example.com', auth=OAuth2ClientCredentials('https://www.token.url', client_id='id', client_secret='secret'))
```

#### Parameters

| Name               | Description                                  | Mandatory | Default value |
|:-------------------|:---------------------------------------------|:----------|:--------------|
| `token_url`        | OAuth 2 token URL.                           | Mandatory |               |
| `client_id`         | Resource owner user name.                    | Mandatory |               |
| `client_secret`         | Resource owner password.                     | Mandatory |               |
| `timeout`          | Maximum amount of seconds to wait for a token to be received once requested. | Optional | 60            |
| `header_name`      | Name of the header field used to send token. | Optional  | Authorization |
| `header_value`     | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |
| `scope`            | Scope parameter sent to token URL as body. Can also be a list of scopes. | Optional |  |
| `token_field_name` | Field name containing the token.             | Optional  | access_token  |

Any other parameter will be put as body parameter in the token URL.

#### Common providers

Most of [OAuth2](https://oauth.net/2/) Client Credentials Grant providers are supported.

If the one you are looking for is not yet supported, feel free to [ask for its implementation](https://github.com/Colin-b/requests_auth/issues/new).

##### OKTA (OAuth2 Client Credentials)

[OKTA Client Credentials Grant](https://developer.okta.com/docs/guides/implement-client-creds/overview/) providing access tokens is supported.

Use `requests_auth.OktaClientCredentials` to configure this kind of authentication.

```python
import requests
from requests_auth import OktaClientCredentials


okta = OktaClientCredentials(instance='testserver.okta-emea.com', client_id='54239d18-c68c-4c47-8bdd-ce71ea1d50cd', client_secret="secret")
requests.get('http://www.example.com', auth=okta)
```

###### Parameters

| Name                    | Description                | Mandatory | Default value |
|:------------------------|:---------------------------|:----------|:--------------|
| `instance`              | OKTA instance (like "testserver.okta-emea.com"). | Mandatory |               |
| `client_id`             | OKTA Application Identifier (formatted as an Universal Unique Identifier). | Mandatory |               |
| `client_secret`         | Resource owner password.                     | Mandatory |               |
| `authorization_server`  | OKTA authorization server. | Optional | 'default' |
| `timeout`               | Maximum amount of seconds to wait for a token to be received once requested. | Optional | 60 |
| `header_name`           | Name of the header field used to send token. | Optional | Authorization |
| `header_value`          | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |
| `scope`                 | Scope parameter sent in query. Can also be a list of scopes. | Optional | openid |
| `token_field_name`      | Field name containing the token. | Optional | access_token |

Any other parameter will be put as query parameter in the token URL.        

### Implicit flow

Implicit Grant is implemented following [rfc6749](https://tools.ietf.org/html/rfc6749#section-4.2).

Use `requests_auth.OAuth2Implicit` to configure this kind of authentication.

```python
import requests
from requests_auth import OAuth2Implicit

requests.get('http://www.example.com', auth=OAuth2Implicit('https://www.authorization.url'))
```

#### Parameters

| Name                    | Description                | Mandatory | Default value |
|:------------------------|:---------------------------|:----------|:--------------|
| `authorization_url`     | OAuth 2 authorization URL. | Mandatory |               |
| `response_type`         | Value of the response_type query parameter if not already provided in authorization URL. | Optional | token |
| `token_field_name`      | Field name containing the token. | Optional | id_token if response_type is id_token, otherwise access_token |
| `redirect_uri_endpoint` | Custom endpoint that will be used as redirect_uri the following way: http://localhost:<redirect_uri_port>/<redirect_uri_endpoint>. | Optional | ''             |
| `redirect_uri_port`     | The port on which the server listening for the OAuth 2 token will be started. | Optional | 5000 |
| `timeout`               | Maximum amount of seconds to wait for a token to be received once requested. | Optional | 60 |
| `success_display_time`  | In case a token is successfully received, this is the maximum amount of milliseconds the success page will be displayed in your browser. | Optional | 1 |
| `failure_display_time`  | In case received token is not valid, this is the maximum amount of milliseconds the failure page will be displayed in your browser. | Optional | 5000 |
| `header_name`           | Name of the header field used to send token. | Optional | Authorization |
| `header_value`          | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |

Any other parameter will be put as query parameter in the authorization URL.        

Usual extra parameters are:
        
| Name            | Description                                                          |
|:----------------|:---------------------------------------------------------------------|
| `client_id`     | Corresponding to your Application ID (in Microsoft Azure app portal) |
| `nonce`         | Refer to [OpenID ID Token specifications][3] for more details        |
| `prompt`        | none to avoid prompting the user if a session is already opened.     |

#### Common providers

Most of [OAuth2](https://oauth.net/2/) Implicit Grant providers are supported.

If the one you are looking for is not yet supported, feel free to [ask for its implementation](https://github.com/Colin-b/requests_auth/issues/new).

##### Microsoft - Azure Active Directory (OAuth2 Access Token)

[Microsoft identity platform access tokens](https://docs.microsoft.com/en-us/azure/active-directory/develop/access-tokens) are supported.

Use `requests_auth.AzureActiveDirectoryImplicit` to configure this kind of authentication.

```python
import requests
from requests_auth import AzureActiveDirectoryImplicit


aad = AzureActiveDirectoryImplicit(tenant_id='45239d18-c68c-4c47-8bdd-ce71ea1d50cd', client_id='54239d18-c68c-4c47-8bdd-ce71ea1d50cd')
requests.get('http://www.example.com', auth=aad)
```

You can retrieve Microsoft Azure Active Directory application information thanks to the [application list on Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).

###### Parameters

| Name                    | Description                | Mandatory | Default value |
|:------------------------|:---------------------------|:----------|:--------------|
| `tenant_id`             | Microsoft Tenant Identifier (formatted as an Universal Unique Identifier). | Mandatory |               |
| `client_id`             | Microsoft Application Identifier (formatted as an Universal Unique Identifier). | Mandatory |               |
| `response_type`         | Value of the response_type query parameter if not already provided in authorization URL. | Optional | token |
| `token_field_name`      | Field name containing the token. | Optional | access_token |
| `nonce`                 | Refer to [OpenID ID Token specifications][3] for more details | Optional | Newly generated Universal Unique Identifier. |
| `redirect_uri_endpoint` | Custom endpoint that will be used as redirect_uri the following way: http://localhost:<redirect_uri_port>/<redirect_uri_endpoint>. | Optional | ''             |
| `redirect_uri_port`     | The port on which the server listening for the OAuth 2 token will be started. | Optional | 5000 |
| `timeout`               | Maximum amount of seconds to wait for a token to be received once requested. | Optional | 60 |
| `success_display_time`  | In case a token is successfully received, this is the maximum amount of milliseconds the success page will be displayed in your browser. | Optional | 1 |
| `failure_display_time`  | In case received token is not valid, this is the maximum amount of milliseconds the failure page will be displayed in your browser. | Optional | 5000 |
| `header_name`           | Name of the header field used to send token. | Optional | Authorization |
| `header_value`          | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |

Any other parameter will be put as query parameter in the authorization URL.        

Usual extra parameters are:
        
| Name            | Description                                                          |
|:----------------|:---------------------------------------------------------------------|
| `prompt`        | none to avoid prompting the user if a session is already opened.     |

##### Microsoft - Azure Active Directory (OpenID Connect ID token)

[Microsoft identity platform ID tokens](https://docs.microsoft.com/en-us/azure/active-directory/develop/id-tokens) are supported.

Use `requests_auth.AzureActiveDirectoryImplicitIdToken` to configure this kind of authentication.

```python
import requests
from requests_auth import AzureActiveDirectoryImplicitIdToken


aad = AzureActiveDirectoryImplicitIdToken(tenant_id='45239d18-c68c-4c47-8bdd-ce71ea1d50cd', client_id='54239d18-c68c-4c47-8bdd-ce71ea1d50cd')
requests.get('http://www.example.com', auth=aad)
```

You can retrieve Microsoft Azure Active Directory application information thanks to the [application list on Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).

###### Parameters

| Name                    | Description                | Mandatory | Default value |
|:------------------------|:---------------------------|:----------|:--------------|
| `tenant_id`             | Microsoft Tenant Identifier (formatted as an Universal Unique Identifier). | Mandatory |               |
| `client_id`             | Microsoft Application Identifier (formatted as an Universal Unique Identifier). | Mandatory |               |
| `response_type`         | Value of the response_type query parameter if not already provided in authorization URL. | Optional | id_token |
| `token_field_name`      | Field name containing the token. | Optional | id_token |
| `nonce`                 | Refer to [OpenID ID Token specifications][3] for more details | Optional | Newly generated Universal Unique Identifier. |
| `redirect_uri_endpoint` | Custom endpoint that will be used as redirect_uri the following way: http://localhost:<redirect_uri_port>/<redirect_uri_endpoint>. | Optional | ''             |
| `redirect_uri_port`     | The port on which the server listening for the OAuth 2 token will be started. | Optional | 5000 |
| `timeout`               | Maximum amount of seconds to wait for a token to be received once requested. | Optional | 60 |
| `success_display_time`  | In case a token is successfully received, this is the maximum amount of milliseconds the success page will be displayed in your browser. | Optional | 1 |
| `failure_display_time`  | In case received token is not valid, this is the maximum amount of milliseconds the failure page will be displayed in your browser. | Optional | 5000 |
| `header_name`           | Name of the header field used to send token. | Optional | Authorization |
| `header_value`          | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |

Any other parameter will be put as query parameter in the authorization URL.        

Usual extra parameters are:
        
| Name            | Description                                                          |
|:----------------|:---------------------------------------------------------------------|
| `prompt`        | none to avoid prompting the user if a session is already opened.     |

##### OKTA (OAuth2 Implicit Access Token)

[OKTA Implicit Grant](https://developer.okta.com/docs/guides/implement-implicit/overview/) providing access tokens is supported.

Use `requests_auth.OktaImplicit` to configure this kind of authentication.

```python
import requests
from requests_auth import OktaImplicit


okta = OktaImplicit(instance='testserver.okta-emea.com', client_id='54239d18-c68c-4c47-8bdd-ce71ea1d50cd')
requests.get('http://www.example.com', auth=okta)
```

###### Parameters

| Name                    | Description                | Mandatory | Default value |
|:------------------------|:---------------------------|:----------|:--------------|
| `instance`              | OKTA instance (like "testserver.okta-emea.com"). | Mandatory |               |
| `client_id`             | OKTA Application Identifier (formatted as an Universal Unique Identifier). | Mandatory |               |
| `response_type`         | Value of the response_type query parameter if not already provided in authorization URL. | Optional | token |
| `token_field_name`      | Field name containing the token. | Optional | access_token |
| `nonce`                 | Refer to [OpenID ID Token specifications][3] for more details. | Optional | Newly generated Universal Unique Identifier. |
| `scope`                 | Scope parameter sent in query. Can also be a list of scopes. | Optional | ['openid', 'profile', 'email'] |
| `authorization_server`  | OKTA authorization server. | Optional | 'default' |
| `redirect_uri_endpoint` | Custom endpoint that will be used as redirect_uri the following way: http://localhost:<redirect_uri_port>/<redirect_uri_endpoint>. | Optional | ''             |
| `redirect_uri_port`     | The port on which the server listening for the OAuth 2 token will be started. | Optional | 5000 |
| `timeout`               | Maximum amount of seconds to wait for a token to be received once requested. | Optional | 60 |
| `success_display_time`  | In case a token is successfully received, this is the maximum amount of milliseconds the success page will be displayed in your browser. | Optional | 1 |
| `failure_display_time`  | In case received token is not valid, this is the maximum amount of milliseconds the failure page will be displayed in your browser. | Optional | 5000 |
| `header_name`           | Name of the header field used to send token. | Optional | Authorization |
| `header_value`          | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |

Any other parameter will be put as query parameter in the authorization URL.        

Usual extra parameters are:
        
| Name            | Description                                                          |
|:----------------|:---------------------------------------------------------------------|
| `prompt`        | none to avoid prompting the user if a session is already opened.     |

##### OKTA (OpenID Connect Implicit ID token)

[OKTA Implicit Grant](https://developer.okta.com/docs/guides/implement-implicit/overview/) providing ID tokens is supported.

Use `requests_auth.OktaImplicitIdToken` to configure this kind of authentication.

```python
import requests
from requests_auth import OktaImplicitIdToken


okta = OktaImplicitIdToken(instance='testserver.okta-emea.com', client_id='54239d18-c68c-4c47-8bdd-ce71ea1d50cd')
requests.get('http://www.example.com', auth=okta)
```

###### Parameters

| Name                    | Description                | Mandatory | Default value |
|:------------------------|:---------------------------|:----------|:--------------|
| `instance`              | OKTA instance (like "testserver.okta-emea.com"). | Mandatory |               |
| `client_id`             | OKTA Application Identifier (formatted as an Universal Unique Identifier). | Mandatory |               |
| `response_type`         | Value of the response_type query parameter if not already provided in authorization URL. | Optional | id_token |
| `token_field_name`      | Field name containing the token. | Optional | id_token |
| `nonce`                 | Refer to [OpenID ID Token specifications][3] for more details. | Optional | Newly generated Universal Unique Identifier. |
| `scope`                 | Scope parameter sent in query. Can also be a list of scopes. | Optional | ['openid', 'profile', 'email'] |
| `authorization_server`  | OKTA authorization server. | Optional | 'default' |
| `redirect_uri_endpoint` | Custom endpoint that will be used as redirect_uri the following way: http://localhost:<redirect_uri_port>/<redirect_uri_endpoint>. | Optional | ''             |
| `redirect_uri_port`     | The port on which the server listening for the OAuth 2 token will be started. | Optional | 5000 |
| `timeout`               | Maximum amount of seconds to wait for a token to be received once requested. | Optional | 60 |
| `success_display_time`  | In case a token is successfully received, this is the maximum amount of milliseconds the success page will be displayed in your browser. | Optional | 1 |
| `failure_display_time`  | In case received token is not valid, this is the maximum amount of milliseconds the failure page will be displayed in your browser. | Optional | 5000 |
| `header_name`           | Name of the header field used to send token. | Optional | Authorization |
| `header_value`          | Format used to send the token value. "{token}" must be present as it will be replaced by the actual token. | Optional | Bearer {token} |

Any other parameter will be put as query parameter in the authorization URL.        

Usual extra parameters are:
        
| Name            | Description                                                          |
|:----------------|:---------------------------------------------------------------------|
| `prompt`        | none to avoid prompting the user if a session is already opened.     |

### Managing token cache

To avoid asking for a new token every new request, a token cache is used.

Default cache is in memory but it is also possible to use a physical cache.

You need to provide the location of your token cache file. It can be a full or relative path.

If the file already exists it will be used, if the file do not exists it will be created.

```python
from requests_auth import OAuth2, JsonTokenFileCache

OAuth2.token_cache = JsonTokenFileCache('path/to/my_token_cache.json')
```

## API key in header

You can send an API key inside the header of your request using `requests_auth.HeaderApiKey`.

```python
import requests
from requests_auth import HeaderApiKey

requests.get('http://www.example.com', auth=HeaderApiKey('my_api_key'))
```

### Parameters

| Name                    | Description                    | Mandatory | Default value |
|:------------------------|:-------------------------------|:----------|:--------------|
| `api_key`               | The API key that will be sent. | Mandatory |               |
| `header_name`           | Name of the header field.      | Optional  | "X-API-Key"   |

## API key in query

You can send an API key inside the query parameters of your request using `requests_auth.QueryApiKey`.

```python
import requests
from requests_auth import QueryApiKey

requests.get('http://www.example.com', auth=QueryApiKey('my_api_key'))
```

### Parameters

| Name                    | Description                    | Mandatory | Default value |
|:------------------------|:-------------------------------|:----------|:--------------|
| `api_key`               | The API key that will be sent. | Mandatory |               |
| `query_parameter_name`  | Name of the query parameter.   | Optional  | "api_key"     |

## Basic

You can use basic authentication using `requests_auth.Basic`.

The only advantage of using this class instead of `requests` native support of basic authentication, is to be able to use it in [multiple authentication](#multiple-authentication-at-once).

```python
import requests
from requests_auth import Basic

requests.get('http://www.example.com', auth=Basic('username', 'password'))
```

### Parameters

| Name                    | Description                    | Mandatory | Default value |
|:------------------------|:-------------------------------|:----------|:--------------|
| `username`              | User name.                     | Mandatory |               |
| `password`              | User password.                 | Mandatory |               |

## NTLM

Requires [requests-negotiate-sspi module][4] or [requests_ntlm module][5] depending on provided parameters.

You can use Windows authentication using `requests_auth.NTLM`.

```python
import requests
from requests_auth import NTLM

requests.get('http://www.example.com', auth=NTLM())
```

### Parameters

| Name                    | Description                    | Mandatory | Default value |
|:------------------------|:-------------------------------|:----------|:--------------|
| `username`              | User name.                     | Mandatory if requests_negotiate_sspi module is not installed. In such a case requests_ntlm module is mandatory. |               |
| `password`              | User password.                 | Mandatory if requests_negotiate_sspi module is not installed. In such a case requests_ntlm module is mandatory. |               |

## Multiple authentication at once

You can also use a combination of authentication using `+` as in the following sample:

```python
import requests
from requests_auth import HeaderApiKey, OAuth2Implicit

api_key = HeaderApiKey('my_api_key')
oauth2 = OAuth2Implicit('https://www.example.com')
requests.get('http://www.example.com', auth=api_key + oauth2)
```

[1]: https://pypi.python.org/pypi/requests "requests module"
[2]: http://docs.python-requests.org/en/master/user/authentication/ "authentication parameter on requests module"
[3]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken "OpenID ID Token specifications"
[4]: https://pypi.python.org/pypi/requests-negotiate-sspi "requests-negotiate-sspi module"
[5]: https://pypi.python.org/pypi/requests_ntlm "requests_ntlm module"
