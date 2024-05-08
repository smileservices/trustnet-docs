# c. Recommendation Requests

Clients can make a request for writing a recommendation on behalf of an existing user.

## * Prerequisites 
* Requester user access token by either
having [a client user exported to TrustNet](/5_syncing_client_users) or by having that user [connecting his TrustNet
account through OIDC](/4-using-oidc)
* The subject of the request public persona id
* A contract that involves the two thatalready exists or
was [created in TrustNet](/6.-Reviews-API/b_contracts/#creating-a-new-contract).


##  * Create new request

The client needs to have a `contract id` and the parties `persona ids` involved:

```
POST https://clients.trustnet.app/review-request

# Request Headers
{
    Content-Type: application/json
    Authorization: bearer <user access jwt>
}

# Request Content
{
	"contract_id": uuid <contract uuid>,
	"author_party": str <persona id>,
	"subject_party": str <persona id>,
	"message": str <message>
}

# HTTP 201 Response
```

##  * What will happen next

* The subject party will receive a message on his primary contact (phone or email) with the request message and an
  unique url
* Clicking the unique url will lead the subject to a form where he can write the review.
* Submitting the review form will create a new review that will be attached to the requester persona.
* TrustNet will send notifications for a new published review to clients that implement the hook