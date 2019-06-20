---
layout: twoColumn
section: guides
type: guide
guide:
    name: me-to-me
    step: ‘4’
title:  Creating an ACH transfer
description: Create a transfer by specifying your Customer's bank funding source ID as the source and your Customer’s balance funding source ID as the destination.
---
# Step 4: Initiating a Transfer (Part 1)

Sending funds from the Customer’s checking account to the Dwolla Balance

## Identify Source and Destination For Transfer

Since you are utilizing a `me-to-me` funds flow, the first step is to send funds from the bank to the balance.

- `Source` - Your Customer’s Checking Bank Funding Source
- `Destination` - Your Customer’s Balance Funding Source

## Step 4A: Initiate a Transfer
To initiate a transfer, we will need to specify the source and destination funding source URLs in the _links parameter.

#### Request Parameters

| Parameter | Required | Type   | Description |
|-----------|----------|--------|----------------|
| _links    | yes      | object | A _links JSON object describing the desired source and destination of a transfer. [Reference the Source and Destination object](https://docs.dwolla.com/#source-and-destination-types) to learn more about possible values for source and destination. |
| amount    | yes      | object | An amount JSON object. [Reference the amount JSON object](https://docs.dwolla.com/#amount-json-object) to learn more. |

<ol class = "alerts">
    <li class="alert icon-alert-alert">
      Within a transfer request, Dwolla supports additional optional parameters. These can range from a `facilitator-fee` to collect a portion of the transfer amount that is sent to your Dwolla account,` or `correlationId` to help correlate transfers from end-to-end. For more information on all available transfer request parameters, check out our <a href="https://docs.dwolla.com/#initiate-a-transfer">API reference documentation. </a>
    </li>
</ol>

#### Request and response (view schema in ‘raw’)

```raw
POST https://api-sandbox.dwolla.com/transfers
Accept: application/vnd.dwolla.v1.hal+json
Content-Type: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY
Idempotency-Key: 19051a62-3403-11e6-ac61-9e71128cae77

{
   "_links": {
       "source": {
           "href": "https://api-sandbox.dwolla.com/funding-sources/707177c3-bf15-4e7e-b37c-55c3898d9bf4"
       },
       "destination": {
           "href": "https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e"
       }
   },
   "amount": {
       "currency": "USD",
       "value": "10.00"
    },
}

...

HTTP/1.1 201 Created
Location: https://api-sandbox.dwolla.com/transfers/74c9129b-d14a-e511-80da-0aa34a9b2388
```

```php
<?php
$transfer_request = array (
  '_links' =>
  array (
    'source' =>
    array (
      'href' => 'https://api-sandbox.dwolla.com/funding-sources/b5e68264-7d4d-42a9-88d4-5616c77c6baa',
    ),
    'destination' =>
    array (
      'href' => 'https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e',
    ),
  ),
  'amount' =>
  array (
    'currency' => 'USD',
    'value' => '225.00',
  )
);

$transferApi = new DwollaSwagger\TransfersApi($apiClient);
$transfer = $transferApi->create($transfer_request);

print($transfer); # => https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388
?>
```

```ruby
transfer_request = {
  :_links => {
    :source => {
      :href => "https://api-sandbox.dwolla.com/funding-sources/b5e68264-7d4d-42a9-88d4-5616c77c6baa"
    },
    :destination => {
      :href => "https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e"
    }
  },
  :amount => {
    :currency => "USD",
    :value => "225.00"
  }
}

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
transfer = app_token.post "transfers", transfer_request
transfer.response_headers[:location] # => "https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388"
```

```python
transfer_request = {
  '_links': {
    'source': {
      'href': 'https://api-sandbox.dwolla.com/funding-sources/b5e68264-7d4d-42a9-88d4-5616c77c6baa'
    },
    'destination': {
      'href': 'https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e'
    }
  },
  'amount': {
    'currency': 'USD',
    'value': '225.00'
  }
}

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
transfer = app_token.post('transfers', transfer_request)
transfer.headers['location'] # => 'https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'
```

```javascript
var transferRequest = {
  _links: {
    source: {
      href: 'https://api-sandbox.dwolla.com/funding-sources/b5e68264-7d4d-42a9-88d4-5616c77c6baa'
    },
    destination: {
      href: 'https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e'
    }
  },
  amount: {
    currency: 'USD',
    value: '225.00'
  }
};

appToken
  .post('transfers', transferRequest)
  .then(function(res) {
    res.headers.get('location'); // => 'https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'
  });
```

When the transfer is created, you will receive a `201` HTTP response with an empty response body. You can refer to the location header to retrieve a link to the created transfer resource. We recommend storing this URL or the transferId for future use.

## Step 4B: Handle Webhooks

If you have an active webhook subscription (required in production & optional in Sandbox), you will receive the `customer_bank_transfer_created` webhook immediately after the transfer resource has been created.

## Step 4C: Simulate Bank Transfer Processing

To simulate bank transfer processing in the Dwolla Sandbox environment, navigate to the Sandbox Dashboard. From here, you will want to click the “Process Bank Transfers” button on the top of the screen. Your Sandbox transfer will be moved out of a pending status and moved to a processed status.

<img src="/images/process-bank-transfers.png">

**Production bank transfer processing timing**

While pending bank transfers can be processed at any time in the Sandbox, behavior will vary in production depending on if your application has access to expedited transfers versus standard ACH transfer times. Refer to our [developer resource article](https://developers.dwolla.com/resources/bank-transfer-workflow/processing-times.html) to learn more on transfer timing in production.

## Step 4D: Handle Webhooks

If you have an active webhook subscription (required in production & optional in Sandbox), you will receive the `customer_bank_transfer_completed` webhook when the status of the transfer has changed from pending to processed.

For more information on transfer related webhooks and when they are fired, refer to our [Developer Resource Article](https://developers.dwolla.com/resources/webhook-events.html).

## Step 4E: Verify Status of Transfer

Since ACH transfers in production can take a few days to complete, webhooks are an efficient way to notify you of when a transfer’s status has been updated from `pending` to `processed` to a destination funding source. However, if you want to verify the status of a transfer at any given point in time, you can make a call to the API to retrieve the transfer by its unique id.

```noselect
{
    "_links": {
        "source": {
            "href": "https://api-sandbox.dwolla.com/accounts/30a6cb55-1754-4948-b431-ebe48288ef25",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "account"
        },
        "funding-transfer": {
            "href": "https://api-sandbox.dwolla.com/transfers/6fdd095c-afd7-e811-8111-bec1f96924ed",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "transfer"
        },
        "destination-funding-source": {
            "href": "https://api-sandbox.dwolla.com/funding-sources/AB443D36-3757-44C1-A1B4-29727FB3111C",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "funding-source"
        },
        "self": {
            "href": "https://api-sandbox.dwolla.com/transfers/74c9129b-d14a-e511-80da-0aa34a9b2388",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "transfer"
        },
        "source-funding-source": {
            "href": "https://api-sandbox.dwolla.com/funding-sources/707177c3-bf15-4e7e-b37c-55c3898d9bf4",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "funding-source"
        },
        "destination": {
            "href": "https://api-sandbox.dwolla.com/customers/4e988dba-0a1e-4591-ad04-eab3613e2f83",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "customer"
        }
    },
    "id": "74c9129b-d14a-e511-80da-0aa34a9b2388",
    "status": "processed",
    "amount": {
        "value": "10.00",
        "currency": "USD"
    }
}
```

# Step 5: Initiating a Transfer (Part 2)

Sending funds from the Dwolla Balance to the Customer’s savings account

In the previous step, you initiated a transfer from your Customer’s checking account to their Dwolla Balance funding source. When the funds have successfully transferred to this funding source, you can then complete the second half of the overall transfer by calling the Dwolla API to push funds to their savings account from the Dwolla balance.

To move funds from your Customer’s Dwolla balance to their attached savings account, you will simply repeat the steps from part 1 by specifying different Funding Sources specified for the `source` and `destination`.

#### Request and response (view schema in ‘raw’)

```raw
POST https://api-sandbox.dwolla.com/transfers
Accept: application/vnd.dwolla.v1.hal+json
Content-Type: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY
Idempotency-Key: 19051a62-3403-11e6-ac61-9e71128cae77

{
   "_links": {
       "source": {
           "href": "https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e"
       },
       "destination": {
           "href": "https://api-sandbox.dwolla.com/funding-sources/AB443D36-3757-44C1-A1B4-29727FB3111C"
       }
   },
   "amount": {
       "currency": "USD",
       "value": "10.00"
    },
}

...

HTTP/1.1 201 Created
Location: https://api-sandbox.dwolla.com/transfers/74c9129b-d14a-e511-80da-0aa34a9b2388
```

```php
<?php
$transfer_request = array (
  '_links' =>
  array (
    'source' =>
    array (
      'href' => 'https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e',
    ),
    'destination' =>
    array (
      'href' => 'https://api-sandbox.dwolla.com/funding-sources/AB443D36-3757-44C1-A1B4-29727FB3111C',
    ),
  ),
  'amount' =>
  array (
    'currency' => 'USD',
    'value' => '225.00',
  )
);

$transferApi = new DwollaSwagger\TransfersApi($apiClient);
$transfer = $transferApi->create($transfer_request);

print($transfer); # => https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388
?>
```

```ruby
transfer_request = {
  :_links => {
    :source => {
      :href => "https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e"
    },
    :destination => {
      :href => "https://api-sandbox.dwolla.com/funding-sources/AB443D36-3757-44C1-A1B4-29727FB3111C"
    }
  },
  :amount => {
    :currency => "USD",
    :value => "225.00"
  }
}

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
transfer = app_token.post "transfers", transfer_request
transfer.response_headers[:location] # => "https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388"
```

```python
transfer_request = {
  '_links': {
    'source': {
      'href': 'https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e'
    },
    'destination': {
      'href': 'https://api-sandbox.dwolla.com/funding-sources/AB443D36-3757-44C1-A1B4-29727FB3111Ce'
    }
  },
  'amount': {
    'currency': 'USD',
    'value': '225.00'
  }
}

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
transfer = app_token.post('transfers', transfer_request)
transfer.headers['location'] # => 'https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'
```

```javascript
var transferRequest = {
  _links: {
    source: {
      href: 'https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e'
    },
    destination: {
      href: 'https://api-sandbox.dwolla.com/funding-sources/AB443D36-3757-44C1-A1B4-29727FB3111C'
    }
  },
  amount: {
    currency: 'USD',
    value: '225.00'
  }
};

appToken
  .post('transfers', transferRequest)
  .then(function(res) {
    res.headers.get('location'); // => 'https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'
  });
```

Your Customer’s funds are now en route to their final destination. Refer to the previous step for reference on how to check the transfer status, simulate bank transfer processing, and handle webhooks in the sandbox environment.

<nav class="pager-nav">
    <a href="fetch-funding-source.html">Back: Fetch Funding Source</a>
</nav>
