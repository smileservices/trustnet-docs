# 8. Seamless transition from Client to TrustNet

Managing contacts, reviews and verifications can all be done through TrustNet UI so that Client Apps can save
development resources. Having a seamless transition for users from the Client UI to TrustNet UI can be achieved with the
users access tokens.

## * Authentication Code

The Client can use the user access token in order to retrieve an authentication code for the user.

```
# POST https://auth.trustnet.app/clients/user-authentication-code

# Request Headers
{
    Content-Type: application/json
    Authorization: bearer <client access jwt>
}

# Request Content
{user_access_jwt: <user acess jwt>}

# Response HTTP 200
# Response Content
<authentication code>
```

## * Redirection to client mediated authentication

The client app then returns to the user client a redirect to TrustNet `client-mediation` auth endpoint where the code is
exchanged for a
limited scope access token that the user client will use.

```
# GET https://auth.trustnet.app/auth/client-mediation/<persona_url>?client_user_auth_code=<authentication code>

# Response HTTP 302
```

The user has thus been seamlessly and safely transitioned to TrustNet UI. The user can then go back to the client
easily.