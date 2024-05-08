# 3. Authenticating the client

Once you have created a new client, you will use the `client id` and the `client secret` to authenticate your
application and receive an `access token` for using the APIs.

## * Get client access token

The client application uses the client access token whenever it's interacting with the APIs. The access token is a
standard signed jwt. Clients will authenticate by exchanging the client id/secret for an access token

```

POST auth.trustnet.app/clients/auth

# Request Headers
Content-Type: application/json

# Request Content
{
	"client_id": "xxx", 
    "secret": "yyy"
}

# Response Content
"jwt token string"

```

## * Using the access token
The access tokens (both user and client) are added to the requests as Authorization headers
```
...

# Request Headers
Authorization: bearer <jwt token string>

...
```

## * Important

Upon receiving the access token, the client should verify its signature.