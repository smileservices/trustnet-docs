# 5. Sync existing user usecase

TrustNet allows for clients to sync their users in order to make use of the Reviews & Verifications APIs. Exporting
existing client users to TrustNet returns id, access and refresh tokens. This functionality allows clients to seamlessly
integrate with TrustNet so that the users don't need to take an extra step of making a new account

## * Sync API request

Can sync one or many users at the same time:

```
# POST https://auth.trustnet.app/clients/export-users

# Request Headers
{
    Content-Type: application/json
    Authorization: bearer <client access jwt>
}

# Request Content
[{
	"client_user_id": str,
    "given_name": str,
    "family_name": str,
    "email": str,
    "email_is_verified": bool
}, ...]

# Response Content
[{
    "client_user_id": str,
    "persona_url": str,
    "token_response": {
        "token_type": "bearer",
        "expires_in": 30,
        "id_token": <jwt>,
        "scope": "openid,trust,social_accounts,reviews", //scopes added by default
        "refresh_token": <jwt>,
        "access_token": <jwt>
    }
}, ...]
```

##  * Client side

The client should save the `persona_url` (the public persona id) associated to the existing user along with the refresh
token. 