# b. Contracts

A contract is conceptualizing a relation between at least two real persons. It is a prerequisite for writing or
requesting a recommendation. In basic terms it states an expectation for all the parties involved.

## * Creating a new contract

Contracts are the basis for reviews/recommendations. When creating a contract through the Clients API the request will
also add the parties involved by their TrustNet persona identifiers.

```
# POST https://clients.trustnet.app/contract

# Request Headers
{
    Content-Type: application/json
    Authorization: bearer <client access jwt>
}

# Request Content
{
	"contract": {
		"name": <ex: Repairing Toyota Hilux>,
		"meta": <optional, json obj>,
		"date": "yyyy-mm-dd"
	}, 
	"parties": [
		{
			"persona_url": <trustnet persona identifier>,
			"role": <ex: mechanic>
		},
		{
			"persona_url": <trustnet persona identifier>,
			"role": <ex: customer>
		},
		...
	]
}

# HTTP 201 Response Content
{
	"name": str <provided previously>,
	"meta": obj <provided previously>,
	"date": str <provided previously>,
	"status": int,
	"id": uuid <contract UUID>
}
```

## * Getting the contract parties

Each contract party is linked to a TrustNet user account and one of their persona.

```
GET https://clients.trustnet.app/contract?id=<contract uuid>

# Request Headers
{
    Content-Type: application/json
    Authorization: bearer <client access jwt>
}


# HTTP 200 Response Content
{
	"contract": {
        "name": str,
        "meta": obj,
        "date": str,
        "status": int,
        "id": uuid
    },
	"parties": [{"persona_url": str, "role": str}, ...]
}
```

## * Getting all recommendations of to a specific contract

Will return all recommendations that have been written in a specific contract.

```
GET https://clients.trustnet.app/contract/user-parties-and-reviews?id=<contract uuid>

# Request Headers
{
    Content-Type: application/json
    Authorization: bearer <user access jwt>
}


# HTTP 200 Response Content
[{
    persona_url: str,
    role: str,
    authored_reviews: [{
        rating: int
        meta: obj
        id: UUID
        status: int
        created_at: datetime
        updated_at: datetime
    }, ...]
}, ...]
```



