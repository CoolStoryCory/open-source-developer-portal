---
layout: twoColumn
section: guides
type: guide
guide:
    name: receive-money
    step: '2'
title:  Checking the transfer status in Dwolla's API
description: You can check the status of the newly created transfer by retrieving the transfer by its URL.
---
# Step 2: Check the transfer status

You can check the status of the newly created transfer by retrieving the transfer by its URL.

#### Request and response (view schema in 'raw'):

```raw
GET https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer 0Sn0W6kzNicvoWhDbQcVSKLRUpGjIdlPSEYyrHqrDDoRnQwE7Q

{
  "_links": {
    "cancel": {
      "href": "https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388",
      "type": "transfer"
    },
    "source": {
      "href": "https://api-sandbox.dwolla.com/customers/247b1bd8-f5a0-4b71-a898-f62f67b8ae1c",
      "type": "customer"
    },
    "destination-funding-source": {
      "href": "https://api-sandbox.dwolla.com/funding-sources/b268f6b9-db3b-4ecc-83a2-8823a53ec8b7",
      "type": "funding-source"
    },
    "self": {
      "href": "https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388",
      "type": "transfer"
    },
    "source-funding-source": {
      "href": "https://api-sandbox.dwolla.com/funding-sources/80275e83-1f9d-4bf7-8816-2ddcd5ffc197",
      "type": "funding-source"
    },
    "destination": {
      "href": "https://api-sandbox.dwolla.com/accounts/ab443d36-3757-44c1-a1b4-29727fb3111c",
      "type": "account"
    }
  },
  "id": "d76265cd-0951-e511-80da-0aa34a9b2388",
  "status": "pending",
  "amount": {
    "value": "225.00",
    "currency": "usd"
  },
  "created": "2015-08-05T20:35:26.520Z",
}
```

```ruby
transfer_url = 'https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
transfer = app_token.get transfer_url
transfer.status # => "pending"
```

```javascript
var transferUrl = 'https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388';

appToken
  .get(transferUrl)
  .then(function(res) {
    res.body.status; // => 'pending'
  });
```

```python
transfer_url = 'https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
transfer = app_token.get(transfer_url)
transfer.body['status'] # => 'pending'
```

```php
<?php
$transferUrl = 'https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388';

$transferApi = new DwollaSwagger\TransfersApi($apiClient);
$transfer = $transferApi->by_id($transferUrl)
# Access desired information in response object fields
print($transfer->status) # => pending
?>
```

That’s it! You’ve successfully received money from a user. Please continue to the Webhooks guide for information on implementing notifications for your customers about the transfer.

<nav class="pager-nav">
    <a href="/guides/receive-money/onboarding.html">Back: Create a Customer and transfer</a>
    <a href="/guides/webhooks">Next guide: Webhooks</a>
</nav>
