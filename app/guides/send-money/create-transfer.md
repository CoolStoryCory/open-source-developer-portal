---
layout: twoColumn
section: guides
type: guide
guide:
    name: send-money
    step: '3'
title:  "Step 3: Create a transfer"
description: Use Dwolla's ACH payment APi to facilitate and create transfers within your application or platform.
---
# Step 3: Create a transfer

Create a transfer by specifying your funding source as the `source` and the Customer as the `destination`. If the destination is a Customer, it is recommended that you send to the funding source that belongs to the Customer as opposed to the Customer resource itself (e.g. `https://api-sandbox.dwolla.com/funding-sources/04173e17-6398-4d36-a167-9d98c4b1f1c3`).

#### Request and response (view schema in 'raw')

```raw
POST https://api-sandbox.dwolla.com/transfers
Accept: application/vnd.dwolla.v1.hal+json
Content-Type: application/vnd.dwolla.v1.hal+json
Authorization: Bearer 0Sn0W6kzNicvoWhDbQcVSKLRUpGjIdlPSEYyrHqrDDoRnQwE7Q
{
    "_links": {
        "source": {
            "href": "https://api-sandbox.dwolla.com/funding-sources/5cfcdc41-10f6-4a45-b11d-7ac89893d985"
        },
        "destination": {
            "href": "https://api-sandbox.dwolla.com/customers/c7f300c0-f1ef-4151-9bbe-005005aa3747"
        }
    },
    "amount": {
        "currency": "USD",
        "value": "225.00"
    },
    "metadata": {
        "customerId": "8675309",
        "notes": "For work completed on Sept. 1, 2015"
    }
}

...

HTTP/1.1 201 Created
Location: https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388
```

```ruby
transfer_request = {
  :_links => {
    :source => {
      :href => "https://api-sandbox.dwolla.com/funding-sources/5cfcdc41-10f6-4a45-b11d-7ac89893d985"
    },
    :destination => {
      :href => "https://api-sandbox.dwolla.com/customers/c7f300c0-f1ef-4151-9bbe-005005aa3747"
    }
  },
  :amount => {
    :currency => "USD",
    :value => "225.00"
  },
  :metadata => {
    :customerId => "8675309",
    :notes => "For work completed on Sept. 1, 2015"
  }
}

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
transfer = account_token.post "transfers", transfer_request
transfer.response_headers[:location] # => "https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388"
```

```javascript
var transferRequest = {
  _links: {
    source: {
      href: 'https://api.dwolla.com/funding-sources/5cfcdc41-10f6-4a45-b11d-7ac89893d985'
    },
    destination: {
      href: 'https://api.dwolla.com/customers/c7f300c0-f1ef-4151-9bbe-005005aa3747'
    }
  },
  amount: {
    currency: 'USD',
    value: '225.00'
  },
  metadata: {
    customerId: '8675309',
    notes: 'For work completed on Sept. 1, 2015'
  }
};

accountToken
  .post('transfers', transferRequest)
  .then(function(res) {
    res.headers.get('location'); // => 'https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'
  });
```

```python
transfer_request = {
  '_links': {
    'source': {
      'href': 'https://api.dwolla.com/funding-sources/5cfcdc41-10f6-4a45-b11d-7ac89893d985'
    },
    'destination': {
      'href': 'https://api.dwolla.com/customers/c7f300c0-f1ef-4151-9bbe-005005aa3747'
    }
  },
  'amount': {
    'currency': 'USD',
    'value': '225.00'
  },
  'metadata': {
    'customerId': '8675309',
    'notes': 'For work completed on Sept. 1, 2015'
  }
}

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
transfer = account_token.post('transfers', transfer_request)
transfer.headers['location'] # => 'https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'
```

```php
<?php
$transfer_request = array (
  '_links' => 
  array (
    'source' => 
    array (
      'href' => 'https://api-sandbox.dwolla.com/funding-sources/5cfcdc41-10f6-4a45-b11d-7ac89893d985',
    ),
    'destination' => 
    array (
      'href' => 'https://api-sandbox.dwolla.com/customers/c7f300c0-f1ef-4151-9bbe-005005aa3747',
    ),
  ),
  'amount' => 
  array (
    'currency' => 'USD',
    'value' => '225.00',
  ),
  'metadata' => 
  array (
    'customerId' => '8675309',
    'notes' => 'For work completed on Sept. 1, 2015',
  ),
);

$transferApi = new DwollaSwagger\TransfersApi($apiClient);
$transfer = $transferApi->create($transfer_request);

print($transfer); # => https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388
?>
```

<nav class="pager-nav">
    <a href="fetch-funding-sources.html">Back: Fetch funding sources</a>
    <a href="check-transfer.html">Next step: Check the transfer status</a>
</nav>
