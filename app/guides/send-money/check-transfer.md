---
layout: twoColumn
section: guides
type: guide
guide:
    name: send-money
    step: '4'
title:  "Step 4: Check the transfer status"
---

# Step 4: Check the transfer status

You can check the status of the newly created transfer by retrieving the transfer by its URL.

#### Request and response (view schema in 'raw')
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
      "href": "https://api-sandbox.dwolla.com/accounts/4bb512e4-ad4d-4f7e-bfd0-a232007f21a1",
      "type": "account"
    },
    "funding-transfer": {
      "href": "https://api-sandbox.dwolla.com/transfers/e73f5b8e-e458-e611-80e5-0aa34a9b2388",
      "type": "transfer"
    },
    "self": {
      "href": "https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388",
      "type": "transfer"
    },
    "source-funding-source": {
      "href": "https://api-sandbox.dwolla.com/funding-sources/5cfcdc41-10f6-4a45-b11d-7ac89893d985",
      "type": "funding-source"
    },
    "destination": {
      "href": "https://api-sandbox.dwolla.com/customers/c7f300c0-f1ef-4151-9bbe-005005aa3747",
      "type": "customer"
    }
  },
  "id": "d76265cd-0951-e511-80da-0aa34a9b2388",
  "status": "pending",
  "amount": {
    "value": "42.00",
    "currency": "usd"
  },
  "created": "2015-09-01T19:08:55.500Z",
  "metadata": {
    "customerId": "8675309",
    "notes": "For work completed on Sept. 1, 2015"
  }
}
```
```ruby
transfer_url = 'https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
transfer = account_token.get transfer_url
transfer.status # => "pending"

# Using DwollaSwagger - https://github.com/Dwolla/dwolla-swagger-ruby
transfer = DwollaSwagger::TransfersApi.by_id(transfer_url)
# Access desired information in response object fields
p transfer.status # => pending
```
```javascript
var transferUrl = 'https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388';

accountToken
  .get(transferUrl)
  .then(function(res) {
    res.body.status; // => 'pending'
  });
```
```python
transfer_url = 'https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
fees = account_token.get(transfer_url)
fees.body['stats'] # => 'pending'

# Using dwollaswagger - https://github.com/Dwolla/dwolla-swagger-python
transfers_api = dwollaswagger.TransfersApi(client)
transfer = transfers_api.by_id(transfer_url)
# Access desired information in response object fields
print(transfer.status) # => pending
```
```php
<?php
$transferUrl = 'https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388';

$transfersApi = new DwollaSwagger\TransfersApi($apiClient);

$transfer = $transfersApi->byId($transferUrl);
print($transfer->status); # => "pending"
?>
```

That’s it! You’ve successfully transferred money to a recipient. Please continue to the Webhooks guide for information on implementing notifications for your customers about the status of the transfer.

<nav class="pager-nav">
    <a href="create-transfer.html">Back: Create a transfer</a>
    <a href="/guides/webhooks">Next guide: Webhooks</a>
</nav>