# 4. Using OpenID Connect

OpenID Connect (OIDC) is an identity layer built on top of the OAuth 2.0 protocol. It provides a standardized way for
web and mobile applications to authenticate users and obtain their basic profile information. OIDC offers developers a
secure and straightforward method to implement user authentication and authorization within their applications.

### What is OIDC used for?

OIDC is commonly used in modern web and mobile applications to enable Single Sign-On (SSO) functionality, allowing users
to log in using their existing accounts from popular identity providers like Google, Facebook, or Microsoft. By
leveraging OIDC, developers can streamline the authentication process, enhance security, and provide a seamless user
experience across multiple services and platforms.

### Where to learn more about OIDC?

For more detailed information about OIDC and its specifications, you can refer to the
official [OpenID Connect website](https://openid.net/connect).
Additionally, many online resources, tutorials, and documentation are available to help you
integrate OIDC into your applications effectively.

## * Clients usecases for OIDC

* sign-in existing users
* sign-up new users
* get user access token
* use user access token for Reviews & Verifications APIs

## * TrustNet.app as an OIDC provider

The purpose of using an OIDC provider is to get authorization to their TrustNet persona and to do actions on behalf of
that user. The OIDC configuration data is available at this
url: `https://auth.trustnet.app/.well-known/openid-configuration`. Will return a `config json object`
called `OIDC_CONFIG` from now on.

### ... Get authorization to the user persona

Following the OIDC/Oauth2 protocol, the user will be redirected to the TrustNet authorize URL:

```
https://auth.trustnet.app/authorize?client_id=<client id>&response_type=code&redirect_uri=<client endpoint>&scope=<authorization scope>
```

### ... Get TrustNet user tokens

Once the user authorized one of his personas, TrustNet will return a redirect response to the `redirect_uri` along with
an `authorization code` for the client.

The `authorization code` will be exchanged by the Client backend to get an `authorization token` in return:

```
POST OIDC_CONFIG['token_endpoint'] 

# Request Content
{
  "grant_type": "code",
  "client_id": <client id>,
  "client_secret": <client secret>,
  "redirect_uri": <url encoded redirect uri>,
  "code": <authorization code>,
  "state": <optional|could be a session id>
}

# Response Content
{
  "access_token": <base64 encodeded signed access token>,
  "expires_in": 30,
  "id_token": <base64 encodeded signed id token>,
  "refresh_token": <base64 encodeded signed refresh token>,
  "scope": <scopes list joined by empty spaces>,
  "token_type": "bearer"
}

```

### ... The user ID token

Can be used for authenticating the user into the Client system. Shares data regarding to the last known authentication
method `amr` and time `auth_time`, user persona id `sub` as well as the client id `aud`. User persona is an unique
identifier for a TrustNet persona. One user can have one or more personas.

```
{
  "headerObj": {
    "alg": "RS256",
    "typ": "JWT"
  },
  "payloadObj": {
    "auth_time": <authentication timestamp>,
    "amr": <list of current authentication methods of user>,
    "exp": <expire at>,
    "iss": "trustnet.app",
    "sub": <persona unique id>,
    "aud": <client id>,
    "iat": <issued at timestamp>,
    "nonce": null
  },
  "headerPP": ...,
  "payloadPP": ...,
  "sigHex": ...
}
```

### ... The user access token

The access token will be used for accessing the scoped endpoints. The endpoints url are all available in the oidc
configuration response `OIDC_CONFIG`.

```
{
  "headerObj": {
    "alg": "RS256",
    "typ": "JWT"
  },
  "payloadObj": {
    "iss": "trustnet.app",
    "sub": <user persona unique id>,
    "aud": <client id>,
    "iat": <issued at timestamp>,
    "scope": <scope list joined by blank space>,
    "exp": <expired at timestamp>,
    "name": null
  },
  "headerPP": ...,
  "payloadPP": ...,
  "sigHex": ...
}
```

**important** the `sub` claim of the JWT can be used to access the user persona profile: `https://trustnet.app/p/<sub>`

### ... The refresh token

The refresh token will be sent at the `OIDC_CONFIG['refresh_token']` endpoint in order to receive
new `id token`, `access token` and new `refresh token`. Once a refresh token has been used, it will become invalid and
the new one has to replace it. The request headers must include a valid client authorization token.

```
POST OIDC_CONFIG['refresh_token']

# Request Headers:
Authorization: bearer <client authorization jwt>

# Request Content:
{
	"refresh_token": <refresh token>
}

# Response Content:
{
	"token_type": "bearer",
	"expires_in": 30,
	"id_token": <newly issued ID token>
	"scope": "openid,trust,social_accounts,reviews",
	"refresh_token": <newly issued refresh token>,
	"access_token": <newly issued access token>
}

```

**Important**
Once the refresh token has been used, the existing `access token` will be invalidated.

## * Verify tokens

The Clients should verify the tokens signature so that man-in-the-middle attacks are prevented. For example we'll use `python` and `jose` library:
```
from jose import JWTError, jwt, jwk
import requests

# first, get the OIDC config object
OIDC_CONFIG = requests.get('https://auth.trustnet.app/.well-known/openid-configuration').json()

public_key = None # we'll get this from trustnet
CLIENT_ID = "your client id"
token = "jwt str"

# retrieve the trustnet public key in jwks format
trustnet_jwks = requests.get(OIDC_CONFIG['jwks_uri']).json()
for k in trustnet_jwks['keys']:
    if k["kid"] == 'trustnet.oidc.key':
        public_key = k
if not public_key:
    raise ValueError("Could not find TrustNet public key")

# build the string public key
public_key_str = jwk.construct({
    'e': public_key['e'],
    'n': public_key['n'],
    'kty': public_key['kty'],
    'alg': public_key['alg']
}).public_key()

# if signature is not verified an exception is raised
jwt_decoded = jwt.decode(
    token,
    public_key_str,
    issuer=OIDC_CONFIG['issuer'],
    algorithms=public_key["alg"],
    audience=CLIENT_ID
)

```

## * Use the user access token

Clients will use the user access tokens to authorize requests to the user resources and TrustNet apis:

### ... Available endpoints for user access token

All these endpoints are available in the `OIDC_CONFIG` object:

```
# OIDC_CONFIG response object
{
  ...
  "userinfo_endpoint": "details about the user (persona)",
  "user_socials_endpoint": "the user's linked social accounts (persona)",
  "user_reviews_endpoint": "the user's reviews (persona)",
  "user_trust_endpoint": "the user's trust score (account)",
  ...
}
```

### ... Request with user access JWT

The client requests to the OIDC endpoints will use the JWT as a bearer token:
```
# Request Headers:
Authorization: bearer <user authorization jwt>
```
