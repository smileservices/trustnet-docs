# d. New Recommendation

Clients can use the user access jwt to submit a new recommendation to another user. 

## * Prerequisites 
* Author user access token by either
having [a client user exported to TrustNet](/5_syncing_client_users) or by having that user [connecting his TrustNet
account through OIDC](/4-using-oidc)
* The subject of the request public persona id
* A contract that involves the two that already exists or
was [created in TrustNet](/6.-Reviews-API/b_contracts/#creating-a-new-contract).

```
POST https://clients.trustnet.app/review-request

# Request Headers
{
    Content-Type: application/json
    Authorization: bearer <user access jwt>
}

# Request Content
{
	"contract_id": <uuid>,
	"author_party": <str public persona id>,
	"subject_party": <str public persona id>,
	"rating": <int 0-5>,
	"meta": <obj ex: {"text": "Mi-a placut cum a iesit vopseaua."}>
}
# HTTP 201 Response
```

##  * What will happen next

* The subject party will receive a message on his primary contact (phone or email) that he received a new recommendation
* TrustNet will send notifications for a new published review to clients that implement the hook