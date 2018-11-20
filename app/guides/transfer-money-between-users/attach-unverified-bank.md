---
layout: twoColumn
section: guides
type: guide
guide:
    name: transfer-money-between-users
    step: '3'
title:  Attach an unverified funding source in Dwolla's API
description: Unverified funding sources can only receive funds, not send. You will include actual bank name, routing, and account numbers after prompting your customer within your application.
---

# Step 3: Attach an unverified funding source

Next, we’ll add Jane Merchant’s bank or credit union account as an unverified funding source.  Unverified funding sources can only receive funds, not send.

The example below shows sample bank information, but you will include actual bank name, routing, and account numbers after prompting your customer for this information within your application. Possible values for `bankAccountType` can be either “checking” or “savings”. More detail is available in [API docs](https://docsv2.dwolla.com/#create-a-funding-source-for-a-customer).

```raw
POST https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C/funding-sources
Content-Type: application/vnd.dwolla.v1.hal+json
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer 0Sn0W6kzNicvoWhDbQcVSKLRUpGjIdlPSEYyrHqrDDoRnQwE7Q
{
    "routingNumber": "222222226",
    "accountNumber": "123456789",
    "bankAccountType": "checking",
    "name": "Jane Merchant"
}

HTTP/1.1 201 Created
Location: https://api-sandbox.dwolla.com/funding-sources/375c6781-2a17-476c-84f7-db7d2f6ffb31
```

```ruby
customer_url = 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C'
request_body = {
  routingNumber: '222222226',
  accountNumber: '123456789',
  bankAccountType: 'checking',
  name: 'Jane Merchant'
}

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
funding_source = app_token.post "#{customer_url}/funding-sources", request_body
funding_source.response_headers[:location] # => "https://api-sandbox.dwolla.com/funding-sources/375c6781-2a17-476c-84f7-db7d2f6ffb31"
```

```javascript
var customerUrl = 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C';
var requestBody = {
  'routingNumber': '222222226',
  'accountNumber': '123456789',
  'bankAccountType': 'checking',
  'name': 'Jane Merchant'
};

appToken
  .post(`${customerUrl}/funding-sources`, requestBody)
  .then(res => res.headers.get('location')); // => 'https://api-sandbox.dwolla.com/funding-sources/375c6781-2a17-476c-84f7-db7d2f6ffb31'
```

```python
customer_url = 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C'
request_body = {
  'routingNumber': '222222226',
  'accountNumber': '123456789',
  'bankAccountType': 'checking',
  'name': 'Jane Merchant'
}

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
customer = app_token.post('%s/funding-sources' % customer_url, request_body)
customer.headers['location'] # => 'https://api-sandbox.dwolla.com/funding-sources/375c6781-2a17-476c-84f7-db7d2f6ffb31'
```

```php
<?php
$fsApi = new DwollaSwagger\FundingsourcesApi($apiClient);

$customer = 'https://api.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C/funding-sources'
$new_fs = $fsApi->createCustomerFundingSource(array (
  'routingNumber' => '222222226',
  'accountNumber' => '123456789',
  'bankAccountType' => 'checking',
  'name' => 'Jane Merchant',
  ), $customer
);

print($new_fs); # => https://api-sandbox.dwolla.com/funding-sources/375c6781-2a17-476c-84f7-db7d2f6ffb31
?>
```

The created funding source URL is returned in the location header.

<nav class="pager-nav">
    <a href="./create-verified-customer.html">Back: Create a verified customer</a>
    <a href="create-unverified-customer.html">Next step: Create an unverified customer</a>
</nav>
