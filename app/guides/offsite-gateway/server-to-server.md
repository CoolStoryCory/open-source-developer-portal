---
layout: twoColumn
section: guides
type: guide
guide: 
    name: offsite-gateway
    step: '2'
title: Off-Site Gateway
description:  Obtain an OAuth access token, used to access data in the Dwolla API on behalf of a user or application.
---

# Off-Site Gateway Checkout

## Server-to-server submission

In order to initialize server-to-server submission, the merchant website must POST a request to the Off-Site Gateway API providing a JSON formatted purchase order placed in the body of the request.

### Creating a request

#### HTTP request
`POST https://www.dwolla.com/oauth/rest/offsitegateway/checkout`

##### Request parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| client_id | yes | string | Your Application Key |
| client_secret | yes | string | Your Application Secret |
| callback | yes | string | Dwolla will POST a Callback containing results of the checkout when it is completed. |
| redirect | yes | string | User will be redirected to this URL after successfully checking out, or cancelling. |
| purchaseOrder | yes | object | A purchaseOrder JSON object. See below. |
| checkoutWithApi | no | boolean | Create a *PayLater* checkout.  Defaults to `false`. |
| allowGuestCheckout | no | boolean | Enable [Dwolla Direct](https://developers.dwolla.com/dev/pages/gateway/direct) experience for users without a Dwolla account |
| orderId | no | string | Custom string to identify the checkout.  Not persisted in resulting transaction. |
| allowFundingSources | no | boolean | Allow the user to pay with a funding source other than their account Balance.  Defaults to `false`. |
| additionalFundingSources | no | string | If `allowFundingSources` enabled, use this to filter the types of funding sources allowed. <br> `banks`: Only allow bank (ACH) funding sources <br> `fisync`: Only allow FiSync-enabled bank funding sources <br> `realtime`: Only allow `fisync` sources <br> `true`: Allow all funding source types <br> `false`: Do not allow any funding sources |

##### PurchaseOrder object
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| destinationId | yes | string | Dwolla ID of the user who will receive the payment in this checkout. |
| total | yes | decimal | Total checkout amount. |
| tax | no | decimal | Order tax total.  For order display purposes only, not persisted. |
| discount | no | decimal | Order discount (must be negative).  For order display purposes only, not persisted. |
| shipping | no | decimal | Order shipping total.  For order display purposes only, not persisted. |
| notes | no | string | A note of max 250 chars. to attach to the resulting payment. |
| facilitatorAmount | no | decimal | Amount of the facilitator fee to override. Only applicable if the facilitator fee feature is enabled. If set to `0`, facilitator fee is disabled for transaction. |
| metadata | no | object | An object containing up to 10 key-value pairs of your choice which is persisted with the resulting transaction.  [Learn about Metadata](https://docs.dwolla.com/#metadata) |
| orderItems | no | array | An array of JSON object(s) which contain a `name`, `description`, `quantity`, and `price`. |

### Finishing checkout

#### Request URL
`https://www.dwolla.com/payment/checkout/{CheckoutId}`

The merchant's website will now need to redirect the user to the checkout endpoint providing the unique checkoutID within 5 minutes. After 5 minutes, the checkout ID will expire and a new request will need to be generated. Depending on the checkout flow used, the user will either complete the transaction on the Dwolla gateway and be redirected to a confirmation page on the merchant's site, or will be redirected to the merchant's site in order to complete the order and fulfill payment.

### Example using Dwolla's v1 libraries
The Dwolla API v1 [libraries](https://docs.dwolla.com/#helper-libraries) available on GitHub have implemented the server-to-server checkout flow. The code samples below are designed to help get you started immediately. In order to customize data sent and used by the library, use the [server-to-server](/guides/server-to-server/) specification and follow the pay now flow.

```raw
POST https://www.dwolla.com/oauth/rest/offsitegateway/checkouts
Content-Type: application/json
{
  "client_id": "JCGQXLrlfuOqdUYdTcLz3rBiCZQDRvdWIUPkw++GMuGhkem9Bo",
  "client_secret": "g7QLwvO37aN2HoKx1amekWi8a2g7AIuPbD5C/JSLqXIcDOxfTr",
  "callback": "http://www.something.com/callback",
  "redirect": "http://www.something.com/return",
  "allowGuestCheckout": "true",
  "checkoutWithApi": "false",
  "orderId": "foo123",
  "allowFundingSources": "true",
  "additionalFundingSources": "true",
  "purchaseOrder": {
        "orderItems": [
            {
                "name": "Some item", 
                "description": "This is a description of some item", 
                "quantity": "1", 
                "price": "15.00"
            }
        ],
    "destinationId": "812-740-4294",
    "total": 15.00,
    "notes": "for the purchase of some item",
    "metadata": {
        "key1": "some value",
        "key2": "another value"
    }
  }
}
``` 
```python
# Using dwolla-python - https://github.com/Dwolla/dwolla-python
from dwolla import checkouts

# Step 1: Create checkout session with items from
# http://docs.dwolla.com/#checkouts

test = checkouts.create({
    'orderItems': {
        frozenset({
            'name': 'Some item',
            'description': 'This is a description of some item',
            'quantity': '1',
            'price': '15.00'
        })
    },
    'destinationId': '812-740-4294',
    'total': 15.00,
    'notes': 'for the purchase of some item',
    'metadata': frozenset({
        'key1': 'some value',
        'key2': 'another value'
    })})

# Step 2: Verify the recently created checkout

checkouts.get(test['CheckoutId'])

# Step 3: Complete the checkout

checkouts.complete('Order ID here')

# Step 4: Verify gateway signature

checkouts.verify('YOUR SIGNATURE HERE', 'YOUR CHECKOUT ID HERE', 4.50)
```
```javascript
// Using dwolla-node - https://github.com/Dwolla/dwolla-node
var cfg = require('./_config')    // Include any required keys
    , dwolla = require('dwolla-node')(cfg.apiKey, cfg.apiSecret)    // Include the Dwolla REST Client
    , $ = require('seq')
    , express = require('express')
    , app = express()
    , util = require('util')
    ;

// Some constants...
var redirect_uri = 'http://localhost:3000/redirect';

// Use Dwolla Sandbox API
dwolla.sandbox = true;

/**
 * EXAMPLE 1: (simple example)
 *   Create a new standard offsite gateway checkout
 **/
app.get('/', function(req, res) {
    var purchaseOrder = {
     destinationId: '812-740-4294',
     total: '5.00'
    };

    var params = {
     allowFundingSources: true,
     orderId: '123456789',
    };

    dwolla.createCheckout(redirect_uri, purchaseOrder, params, function(err, checkout) {
     if (err) console.log(err);

      // example checkout result:
      // { checkoutURL: 'https://uat.dwolla.com/payment/checkout/d0429a55-c338-4139-b273-48d2f8c45693',
      //   checkoutId: 'd0429a55-c338-4139-b273-48d2f8c45693' }

     res.send(util.format('Checkout created.  Click to continue: <a href="%s">%s</a>', checkout.checkoutURL, checkout.checkoutURL));
    });
});
```
```ruby
# Using dwolla-ruby - https://github.com/Dwolla/dwolla-ruby
# Include the Dwolla gem
require 'rubygems'
require 'pp'
require 'dwolla'

# Include any required keys
require './_keys.rb'

# Instantiate a new Dwolla User client
Dwolla::api_key = @api_key
Dwolla::api_secret = @api_secret

# Clear out any previous session data
Dwolla::OffsiteGateway.clear_session

# Optional Settings
Dwolla::OffsiteGateway.redirect = 'http://dwolla.com/payment_redirect'
Dwolla::OffsiteGateway.callback = 'http://dwolla.com/payment_callback'
Dwolla::OffsiteGateway.set_customer_info('Jane', 'Doe', 'janedoe@email.com', 'New York', 'NY', '10001')
Dwolla::OffsiteGateway.discount = 4.95
Dwolla::OffsiteGateway.notes = 'This is just an offsite gateway test...'

# Add products
Dwolla::OffsiteGateway.add_product('Some product', 'This is a description of some product', 499.99, 1)

# Generate a checkout sesssion
pp Dwolla::OffsiteGateway.get_checkout_url('812-734-7288')

# Verify and parse gateway callback
data = Dwolla::OffsiteGateway.read_callback('{"Amount":0.01,"OrderId":null,"Status":"Completed","Error":null,"TransactionId":3396300,"CheckoutId":"51f9dfaa-20ed-41a7-8874-00a47c19c655","Signature":"8be03dfd0e95c567b2855f5876acfc98992f6402","TestMode":"false","ClearingDate":"7/22/2013 8:42:18 PM"}')
pp data
```
```php
<?php
/**
 * Using dwolla-php - https://github.com/Dwolla/dwolla-php
 * The following is a quick-start example for the Checkouts class,
 * which encapsulates methods for all offsite-gateway related endpoints.
 */
// We need the Checkouts class in order to do anything
require '../lib/checkouts.php';
$Checkouts = new Dwolla\Checkouts();
/**
 * Optional: DwollaPHP has a very rudimentary shopping-cart
 * function. You can "make" your own cart and the
 * library will automatically calculate totals for you.
 * For the purpose of this example, we will add two
 * cups of coffee to our cart.
 */
$Checkouts->addToCart("Coffee 1", "Mmm, fresh!", 2.25, 1);
$Checkouts->addToCart("Coffee 2", "Another coffee!", 2.25, 1);
/**
 * Step 1: Create a checkout session with the cart you have just created.
 * The array passed in contains members of the
 * `purchaseOrder` parameter. The second array marks this as a
 * `test` in order to...well...test.
 */
$test = $Checkouts->create(
    [ 'destinationId' => '812-111-7219' ],
    [ 'redirect' => 'http://requestb.in/1fglpx81' ]
);
print_r($test);
/**
 * Step 2: Verify gateway signature
 */
print_r($Checkouts->verify('YOUR SIGNATURE HERE', 'YOUR CHECKOUT ID HERE', 4.50));
?>
```

## Implementing the pay now flow
Implementation is dependent on which checkout flow is used. We're going to walk you through what you need to do to get started with the pay now flow.

### **1. Handling the `callback`**

If callback is set with a callback URL, Dwolla will POST an application/json request to your URL with some information:

| Parameter | Description |
|-----------|-------------|
| Amount | Total amount of purchase formatted to two decimal places. |
| CheckoutID | Unique purchase order ID generated by the off-site gateway. |
| ClearingDate | Date and time in which the funds are to be cleared into the destination account. ClearingDate will return a ISO-8601 formatted timestamp. |
| Error | Any error(s) that occurred during checkout. |
| OrderId | Order ID provided during the request. |
| Signature | HMAC-SHA1 hexadecimal hash of the checkoutId and amount ampersand separated using the consumer secret of the application as the hash key. |
| Status | Status of the checkout session. Possible values: Completed, and Failed. |
| TestMode | Set to 'true' if purchase order is for testing purposes only, otherwise 'false'. |
| TransactionId | Dwolla transaction ID to identify the successful transaction. If in test mode, the transaction ID has a value of 'null'. |

##### Example HTTP Post
```noselect
POST https://www.example.com/payment_callback HTTP/1.1
Content-Type: application/json
Content-Length: 252

{
  "Amount": 0.01,
  "CheckoutId": "f32b1e55-9612-4b6d-90f9-1c1519e588da",
  "ClearingDate": "8/28/2012 3:17:18 PM",
  "Error": null,
  "OrderId": null,
  "Signature": "c50e4b2a4c50ad795c3ee31370aec1a563565aa4",
  "Status": "Completed",
  "TestMode": "false",
  "TransactionId": 1312616
}
```

### **2. Handling the user redirect**

When the user is redirected back to the specified merchant's website in redirect, information will be included in the redirect URL's querystring parameters:

| Parameter | Description |
|-----------|-------------|
| checkoutId | Unique purchase order ID generated by Off-Site Gateway. |
| orderId | Order ID provided during the request. |
| test | Flagged to 'true' if purchase order is for testing purposes only, absent otherwise. |
| transaction | Dwolla transaction ID to identify the successful transaction. If in test mode, the transaction ID has a value of 'null'. |
| postback | If the callback URL provided and the postback response completed succesfully, a value of "success". If the callback URL provided is not valid and the postback response completed unsuccessfully, a value of "failed". |
| amount | Total amount of the purchase formatted to two decimal places. |
| signature | HMAC-SHA1 hexadecimal hash of the checkoutId and amount ampersand separated using the consumer secret of the application as the hash key. |
| clearingDate | Date and time in which the funds are to be cleared into the destination account. ClearingDate will return a ISO-8601 formatted timestamp. |
| status | Status of the checkout session. Possible values: Completed, and Failed. |

##### URL example - success and failure

```noselect
// Success
Format: https://www.myurl.com/redirect?signature={}&orderId={}&amount={}&checkoutId={}&status={}&clearingDate={}&transaction={}&postback={}

Example: https://www.myurl.com/redirect?signature=c50e4b2a4c50ad795c3ee31370aec1a563565aa4&orderId=&amount=0.01&checkoutId=f32b1e55-9612-4b6d-90f9-1c1519e588da&status=Completed&clearingDate=8/28/2012%203:17:18%20PM&transaction=1312616&postback=success

// Failure
Format: https://www.myurl.com/redirect?checkoutId={}&error=failure&error_description={}

Example: https://www.myurl.com/redirect?checkoutId=694e6bcb-349f-451d-bf5d-8aa16530c960&error=failure&error_description=There+are+insufficient+funds+for+this+transaction

Possible error_descriptions:
"Payment has already been generated for application, timestamp, and order ID."
"Invalid callback URL"
"Invalid application signature."
"Invalid timestamp."
"Invalid application credentials."
"There are insufficient funds for this transaction."
"User Cancelled"
"Invalid destination user."
```

### **3. Handling verification / security - recommended**

In both the user redirect querystring, and the callback POST request, Dwolla includes a signature that can be used to authenticate Dwolla's server identity. It is not required to do this, but highly recommended in order to ensure the safety of both you, the merchant, and the customer. The signature is an HMAC-SHA1 hexadecimal hash of the checkoutId and amount ampersand separated using the consumer secret of the application as the hash key.

##### Example signature verification
```raw
not applicable
``` 
```python
def verify_gateway_signature(proposed_signature, checkout_id, amount):
  import hmac
  import hashlib

  raw = '%s&%.2f' % (checkout_id, amount)
  signature = hmac.new(client_secret, raw, hashlib.sha1).hexdigest()

  return True if (signature == proposed_signature) else False
```
```javascript
var verifyGatewaySignature = function(proposed_signature, checkout_id, amount) {
  var crypto    = require('crypto')
  , secret      = 'API_SECRET_HERE'
  , text        = checkout_id + '&' + amount + '&' + order_id
  , hash;

hash = crypto.createHmac('sha1', secret).update(text).digest('hex');

return proposed_signature === hash;
}
```
```ruby
def self.valid_dwolla_signature?(proposed_signature, checkout_id, amount)
  require 'base64'
  require 'openssl'

  raw = "#{checkout_id}&#{amount}"
  digest  = OpenSSL::Digest::Digest.new('sha1')
  signature = OpenSSL::HMAC.hexdigest(digest, client_secret, raw)
  return (signature == proposed_signature)
end
```
```php
<?php
function verifyGatewaySignature($proposedSignature, $checkoutId, $amount) {
    $amount = number_format($amount, 2);
    $signature = hash_hmac("sha1", "{$checkoutId}&{$amount}", $apiSecret);

    return $signature == $proposedSignature;
}
?>
```

<nav class="pager-nav">
    <a href="./">Back: Overview</a>
    <a href="" style="display:none;"></a>
</nav>