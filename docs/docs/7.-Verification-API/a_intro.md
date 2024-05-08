# a. Intro

**This API is work in progress, not yet released**

Verifications API allows clients to handle identity verifications through TrustNet just by making a simple API call for
asking the user to verify his identity or other document. The completed verifications of the user will be available on
the `/user/trust` endpoint:

```
# GET https://clients.trustnet.app/user/trust
# Request Headers
{
    Content-Type: application/json
    Authorization: bearer <user access jwt>
}

# Response content
{
	"socials": [
		{
			"name": "github_profile",
			"score": 82,
			"date": "2023-10-09T19:57:58.237597"
		}
	],
	"verifications": [{
			"attributes": "alias, given_name, family_name, business_owner",
			"document": "Business Registration",
			"issued-by": "Romanian Minister of Finance",
			"verification_status": "completed",
			"created_at": "2023-10-11T11:17:27.202938",
			"updated_at": "2023-10-11T11:17:27.202944"
		}, {
			"attributes": "given_name, family_name, address",
			"document": "National ID",
			"issued-by": "Romanian Interior Ministry",
			"verification_status": "completed",
			"created_at": "2023-10-11T11:17:27.202938",
			"updated_at": "2023-10-11T11:17:27.202944"
		}, ...],
	"score": 182
}
```

##     * Prerequisites

* User access token by either
  having [a client user exported to TrustNet](/5_syncing_client_users) or by having that user [connecting his TrustNet
  account through OIDC](/4-using-oidc)

##     * Initiate the verification request

Client will make a request to `https://clients.trustnet.app/verification` with the required verification scope.

**... in progress**

