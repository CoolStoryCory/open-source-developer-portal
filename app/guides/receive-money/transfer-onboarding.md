---
layout: twoColumn
section: guides
guide: 
    name: receive-money
    step: 1b
type: guide
title:  "Step 1: Transfer onboarding"
---

# Step 1: Create a Dwolla Direct account for the payer

In this experience, the end user is sent to Dwolla to create an account and then returned to your application upon completing the OAuth flow. If you prefer that your customers not create Dwolla accounts, choose the Access API.

### Step A. Construct OAuth authorization request URL

Create a URL to send the user to in order to create a new Dwolla Direct account.  When the user has created a Direct account, they’ll be prompted to give your application permission to access their data based on the scopes requested, and if they agree, they will be redirected back to your application.  More detail on implementing the OAuth flow can be found in the [Authorization](/guides/auth/authorization-code-flow.html).

Be sure to request the `Send` and `Funding` scopes in the initial authorization request. By requesting the `Send` and `Funding` scopes Dwolla will prompt the user within the OAuth flow to attach a verified bank, which is needed in order to send money.

**URL Format:**

`https://www.dwolla.com/oauth/v2/authenticate?client_id={client_id}&response_type=code&redirect_uri={redirect_uri}&scope={scope}`

**Example URL:**

`https://uat.dwolla.com/oauth/v2/authenticate?client_id=PO%2BSzGAsZCE4BTG7Cw4OAL40Tpf1008mDjGBSVo6QLNfM4mD%2Ba&response_type=code&redirect_uri=https://example.com/return&scope=Send%7CTransactions%7CFunding&dwolla_landing=register`

### Step B. Redirect back to your application and generate access token

The customer will complete their account registration and attach a verified funding source.  After that, they will be prompted to grant your application permission transfer money on their behalf.  Once the customer grants authorization, they’ll be redirected back to the redirect_uri you specified in the previous step with a `code` querystring parameter -- this is an authorization code.  The last step in the OAuth process is to [exchange this authorization code](https://docsv2.dwolla.com/transfer/#account-authorization) for an access token and refresh token pair.

#### Example redirect with authorization code:

`https://example.com/return?code=sZCE4BTG7Cw4O`

```rawnoselect
POST https://uat.dwolla.com/oauth/v2/token
Content-Type: application/json

{
  "client_id": "JCGQXLrlfuOqdUYdTcLz3rBiCZQDRvdWIUPkw++GMuGhkem9Bo",
  "client_secret": "g7QLwvO37aN2HoKx1amekWi8a2g7AIuPbD5C/JSLqXIcDOxfTr",
  "code": "sZCE4BTG7Cw4O",
  "grant_type": "authorization_code",
  "redirect_uri": "https://example.com/return"
}
```

#### Response:

```jsonnoselect
{
  "_links": {
    "account": {
      "href": "https://api-uat.dwolla.com/accounts/ca32853c-48fa-40be-ae75-77b37504581b"
    }
  },
  "access_token": "2U2HdYXyQfdZN4hQeQKstadbmqC40mrsVMmzi6Up62R36eFTHW",
  "expires_in": 3600,
  "refresh_token": "aClhbl9euHAq31ldke51DcN0ml2ZAAfBIT7PDhyYXoLCEtGQHO",
  "refresh_expires_in": 5184000,
  "token_type": "bearer",
  "scope": "transactions|send|funding",
  "account_id": "ca32853c-48fa-40be-ae75-77b37504581b"
}
```

### Step C. Get list of the user Account's funding sources

Using the access token we just received, we’ll need to get the funding source ID of the bank account we’d like to use to fund the transfer.  

Use the [List an account's funding sources](https://docsv2.dwolla.com/transfer/#list-funding-sources-for-an-account) endpoint to fetch a list of the payer’s funding sources.  You first need to fetch [the root](https://docsv2.dwolla.com/transfer/#root) resource to determine the URL to get the account’s funding source list from.

```raw
GET https://api-uat.dwolla.com/accounts/ca32853c-48fa-40be-ae75-77b37504581b/funding-sources
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer 2U2HdYXyQfdZN4hQeQKstadbmqC40mrsVMmzi6Up62R36eFTHW

...

{
  "_links": {
    "self": {
      "href": "https://api-uat.dwolla.com/accounts/ca32853c-48fa-40be-ae75-77b37504581b/funding-sources"
    }
  },
  "_embedded": {
    "funding-sources": [
      {
        "_links": {
          "self": {
            "href": "https://api-uat.dwolla.com/funding-sources/04173e17-6398-4d36-a167-9d98c4b1f1c3"
          },
          "account": {
            "href": "https://api-uat.dwolla.com/accounts/ca32853c-48fa-40be-ae75-77b37504581b"
          }
        },
        "id": "04173e17-6398-4d36-a167-9d98c4b1f1c3",
        "status": "verified",
        "type": "bank",
        "name": "Joe Buyer - Checking 1234",
        "created": "2014-07-09T20:39:37.000Z"
      }
    ]
  }
}
```
```ruby
account_url = 'https://api.dwolla.com/accounts/ca32853c-48fa-40be-ae75-77b37504581b'

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
funding_sources = account_token.get "#{account_url}/funding-sources"
funding_sources._embedded['funding-sources'][0].name # => "Joe Buyer - Checking 1234"

# Using DwollaSwagger - https://github.com/Dwolla/dwolla-swagger-ruby
funding_sources = DwollaSwagger::FundingsourcesApi.get_account_funding_sources(account_url)
funding_sources._embedded[:'funding-sources'][0][:name] # => "Joe Buyer - Checking 1234"
```
```php
<?php
$accountUrl = 'https://api.dwolla.com/accounts/ca32853c-48fa-40be-ae75-77b37504581b';

$fsApi = new DwollaSwagger\FundingsourcesApi($apiClient);

$fundingSources = $fsApi->getAccountFundingSources($accountUrl);
$fundingSources->_embedded->{'funding-sources'}[0]->name); # => "Joe Buyer - Checking 1234"
?>
```
```python
account_url = 'https://api.dwolla.com/accounts/ca32853c-48fa-40be-ae75-77b37504581b'

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
funding_sources = account_token.get('%s/funding-sources' % account_url)
funding_sources.body['_embedded']['funding-sources'][0]['name'] # => 'Joe Buyer - Checking 1234'

# Using dwollaswagger - https://github.com/Dwolla/dwolla-swagger-python
fs_api = dwollaswagger.FundingsourcesApi(client)
funding_sources = fs_api.get_account_funding_sources(account_url)
funding_sources._embedded['funding-sources'][0]['name'] # => Joe Buyer - Checking 1234
```
```javascript
var accountUrl = 'https://api-uat.dwolla.com/accounts/ca32853c-48fa-40be-ae75-77b37504581b';

accountToken
  .get(`${accountUrl}/funding-sources`)
  .then(function(res) {
    res.body._embedded['funding-sources'][0].name; // => 'Joe Buyer - Checking 1234'
  });
```

### Step D. Create a transfer to your account

Finally, you can create a transfer from the payer’s bank account to your own account.  


```raw
POST https://api-uat.dwolla.com/transfers
Accept: application/vnd.dwolla.v1.hal+json
Content-Type: application/vnd.dwolla.v1.hal+json
Authorization: Bearer 2U2HdYXyQfdZN4hQeQKstadbmqC40mrsVMmzi6Up62R36eFTHW
{
    "_links": {
        "source": {
            "href": "https://api-uat.dwolla.com/funding-sources/04173e17-6398-4d36-a167-9d98c4b1f1c3"
        },
        "destination": {
            "href": "https://api-uat.dwolla.com/accounts/ab443d36-3757-44c1-a1b4-29727fb3111c"
        }
    },
    "amount": {
        "currency": "USD",
        "value": "225.00"
    }
}

HTTP/1.1 201 Created
Location: https://api-uat.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388
```
```ruby
request_body = {
  :_links => {
    :source => {
      :href => "https://api.dwolla.com/funding-sources/04173e17-6398-4d36-a167-9d98c4b1f1c3"
    },
    :destination => {
      :href => "https://api.dwolla.com/accounts/ab443d36-3757-44c1-a1b4-29727fb3111c"
    }
  },
  :amount => {
    :currency => "USD",
    :value => "225.00"
  },
  :metadata => {
    :foo => "bar",
    :baz => "boo"
  }
}

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
transfer = account_token.post "transfers", request_body
transfer.headers[:location] # => "https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388"

# Using DwollaSwagger - https://github.com/Dwolla/dwolla-swagger-ruby
transfer = DwollaSwagger::TransfersApi.create(:body => request_body)
transfer # => "https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388"
```
```javascript
var requestBody = {
  _links: {
    source: {
      href: 'https://api.dwolla.com/funding-sources/04173e17-6398-4d36-a167-9d98c4b1f1c3'
    },
    destination: {
      href: 'https://api.dwolla.com/accounts/ab443d36-3757-44c1-a1b4-29727fb3111c'
    }
  },
  amount: {
    currency: 'USD',
    value: '225.00'
  },
  metadata: {
    foo: 'bar',
    baz: 'boo'
  }
};

accountToken
  .post('transfers', requestBody)
  .then(function(res) {
    res.headers.get('location'); // => 'https://api.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388s'
  });
```
```python
request_body = {
  '_links': {
    'source': {
      'href': 'https://api.dwolla.com/funding-sources/04173e17-6398-4d36-a167-9d98c4b1f1c3'
    },
    'destination': {
      'href': 'https://api.dwolla.com/accounts/ab443d36-3757-44c1-a1b4-29727fb3111c'
    }
  },
  'amount': {
    'currency': 'USD',
    'value': '225.00'
  },
  'metadata': {
    'foo': 'bar',
    'baz': 'boo'
  }
}

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
transfer = account_token.post('transfers', request_body)
transfer.headers['location'] # => 'https://api-uat.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'

# Using dwollaswagger - https://github.com/Dwolla/dwolla-swagger-python
transfers_api = dwollaswagger.TransfersApi(client)
transfer = transfers_api.create(body = request_body)
transfer # => 'https://api-uat.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'
```
```php
<?php
$transfer_request = array (
  '_links' => 
  array (
    'source' => 
    array (
      'href' => 'https://api-uat.dwolla.com/funding-sources/04173e17-6398-4d36-a167-9d98c4b1f1c3',
    ),
    'destination' => 
    array (
      'href' => 'https://api-uat.dwolla.com/accounts/ab443d36-3757-44c1-a1b4-29727fb3111c',
    ),
  ),
  'amount' => 
  array (
    'currency' => 'USD',
    'value' => '225.00',
  )
);

$transferApi = new DwollaSwagger\TransfersApi($apiClient);
$myAccount = $transferApi->create($transfer_request);

print($xfer); # => https://api-uat.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388
?>
```

<nav class="pager-nav">
    <a href="./">Back: Overview</a>
    <a href="check-transfer.html">Next step: Check transfer status</a>
</nav>