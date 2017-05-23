---
layout: twoColumn
section: Customer verification
type: article
title:  "Business verified Customers"
weight: 1
description: "How to verify a customer before sending a bank transfer with Dwolla's ACH API."
---

# Business verified Customers

### Create a verified business Customer

To create a verified business Customer, use the [create a customer](https://docsv2.dwolla.com/#create-a-customer) endpoint:

```raw
POST https://api-sandbox.dwolla.com/customers
Content-Type: application/vnd.dwolla.v1.hal+json
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer 0Sn0W6kzNicvoWhDbQcVSKLRUpGjIdlPSEYyrHqrDDoRnQwE7Q
{
  "firstName": "Jane",
  "lastName": "Merchant",
  "email": "janeMerchant@email.com",
  "ipAddress": "127.0.0.1",
  "type": "business",
  "address": "99-99 33rd St",
  "city": "Some city",
  "state": "NY",
  "postalCode": "11101",
  "dateOfBirth": "1970-01-01",
  "ssn": "1234",
  "businessClassification": "9ed38155-7d6f-11e3-83c3-5404a6144203",
  "businessType": "llc",
  "businessName":"Jane Corp",
  "ein":"12-3456789"
}

HTTP/1.1 201 Created
Location: https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C
```
```ruby
request_body = {
  :firstName => 'Jane',
  :lastName => 'Merchant',
  :email => 'janeMerchant@email.com',
  :type => 'business',
  :address1 => '99-99 33rd St',
  :city => 'Some City',
  :state => 'NY',
  :postalCode => '11101',
  :dateOfBirth => '1970-01-01',
  :ssn => '1234',
  :businessClassification => '9ed38155-7d6f-11e3-83c3-5404a6144203',
  :businessType => 'llc',
  :businessName => 'Jane Corp',
  :ein => '12-3456789',
}
# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
customer = app_token.post "customers", request_body
customer.headers[:location] # => "https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C"

# Using DwollaSwagger - https://github.com/Dwolla/dwolla-swagger-ruby
customer = DwollaSwagger::CustomersApi.create(:body => request_body)
customer # => "https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C"
```
```javascript
var requestBody = {
  firstName: 'Jane',
  lastName: 'Merchant',
  email: 'janeMerchant@email.com',
  type: 'business',
  address1: '99-99 33rd St',
  city: 'Some City',
  state: 'NY',
  postalCode: '11101',
  dateOfBirth: '1970-01-01',
  ssn: '1234',
  businessClassification: '9ed38155-7d6f-11e3-83c3-5404a6144203',
  businessType: 'llc',
  businessName: 'Jane Corp',
  ein: '12-3456789'
};

appToken
  .post('customers', requestBody)
  .then(res => res.headers.get('location')); // => 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C'
```
```python
request_body = {
  'firstName': 'Jane',
  'lastName': 'Merchant',
  'email': 'janeMerchant@email.com',
  'type': 'business',
  'address1': '99-99 33rd St',
  'city': 'Some City',
  'state': 'NY',
  'postalCode': '11101',
  'dateOfBirth': '1970-01-01',
  'ssn': '1234',
  'businessClassification': '9ed38155-7d6f-11e3-83c3-5404a6144203',
  'businessType': 'llc',
  'businessName': 'Jane Corp',
  'ein': '12-3456789'
}

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
customer = app_token.post('customers', request_body)
customer.headers['location'] # => 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C'

# Using dwollaswagger - https://github.com/Dwolla/dwolla-swagger-python
customers_api = dwollaswagger.CustomersApi(client)
customer = customers_api.create(body = request_body)
customer # => 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C'
```
```php
<?php
$customersApi = new DwollaSwagger\CustomersApi($apiClient);

$new_customer = $customersApi->create([
  'firstName' => 'Jane',
  'lastName' => 'Merchant',
  'email' => 'janeMerchant@email.com',
  'type' => 'business',
  'address' => '99-99 33rd St',
  'city' => 'Some City',
  'state' => 'NY',
  'postalCode' => '11101',
  'dateOfBirth' => '1970-01-01',
  'ssn' => '1234',
  'businessClassification' => '9ed38155-7d6f-11e3-83c3-5404a6144203',
  'businessType' => 'llc',
  'businessName' => 'Jane Corp',
  'ein' => '12-3456789'
]);

print($new_customer); # => https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C
?>
```

You’ll need to provide the the a full name, email address, home address, date of birth, and the last four digits of the Social Security Number for the authorized representative of the business. In addition to the authorized representative’s identifying information, you’ll also need to supply information which helps Dwolla identify the business. Required information includes: `businessClassification`, `businessType`, `businessName`, and `ein`.

Once you submit this request, Dwolla will perform some initial validation to check for formatting issues such as an invalid date of birth, invalid email format, etc. If successful, the response will be a HTTP 201/Created with the URL of the new Customer resource contained in the `Location` header.

### Check the status of the business Customer

Businesses may need to provide additional information to help verify their identity which will likely correspond to a verification status of `document` upon creation. It is important to immediately check the status of the business Customer to determine if additional documentation is needed. When a Customer has been successfully verified by Dwolla, their status will be set to `verified`.

Let’s check to see if the Customer was successfully verified or not. We are going to use the location of the Customer resource that we just created, which is in `new_customer`.

```raw
GET https://api-sandbox.dwolla.com/customers/07D59716-EF22-4FE6-98E8-F3190233DFB8
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY

{
  "_links": {
    "self": {
      "href": "https://api.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C"
    }
  },
  "id": "AB443D36-3757-44C1-A1B4-29727FB3111C",
  "firstName": "Jane",
  "lastName": "Doe",
  "email": "janedoe@nomail.com",
  "type": "unverified",
  "status": "unverified",
  "created": "2015-09-03T23:56:10.023Z"
}
```
```ruby
customer_url = 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C'

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
customer = app_token.get customer_url
customer.firstName # => "Jane"

# Using DwollaSwagger - https://github.com/Dwolla/dwolla-swagger-ruby
customer = DwollaSwagger::CustomersApi.get_customer(customer_url)
customer.firstName # => "Jane"
```
```php
<?php
$customerUrl = 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C';

$customersApi = new DwollaSwagger\CustomersApi($apiClient);

$customer = $customersApi->getCustomer($customerUrl);
$customer->firstName; # => "Jane"
?>
```
```python
customer_url = 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C'

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
customer = app_token.get(customer_url)
customer.body['firstName']

# Using dwollaswagger - https://github.com/Dwolla/dwolla-swagger-python
customers_api = dwollaswagger.CustomersApi(client)
customer = customers_api.get_customer(customer_url)
customer.firstName # => 'Jane'
```
```javascript
var customerUrl = 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C';

appToken
  .get(customerUrl)
  .then(res => res.body.firstName); // => 'Jane'
```

Our Customer is created successfully, but there is a verification status of “document”. Continue reading for instructions on [handling Customer verification statuses](/resources/customer-verification/handling-verification-statuses.html) and guidelines for providing additional information to verify this type of Customer.

* * *

#### View:

*   [Handling verification statuses](/resources/customer-verification/handling-verification-statuses.html)
