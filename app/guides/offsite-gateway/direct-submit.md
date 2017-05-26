---
layout: twoColumn
section: guides
type: guide
guide: 
    name: offsite-gateway
    step: '1'
title: Off-Site Gateway | Direct Submit
description: Obtain an OAuth access token, used to access data in the Dwolla API on behalf of a user or application. Learn more about how to utilize the direct submit option.
---

# Off-Site Gateway Checkout

## Direct submit

This option is great for cases where there is only one item that needs to be purchased. Common use cases are: donation purposes, paying a one-time subscription fee, or buying a single product. In order to initiate direct submission, a form must be directly submitted to the Off-Site Gateway endpoint `payment/pay` with all required checkout information. The user will be redirected to Dwolla where the checkout process will be completed. 

#### HTTP request
`POST https://www.dwolla.com/payment/pay`

##### Request parameters
| Parameter | Required | Type | Description |
|-----------|----------|----------------|-------------|
| key | yes | string | Application key. |
| signature | yes | string | HMAC-SHA1 hex hash of the app key, timestamp, and order ID. |
| timestamp | yes | string | Timestamp of the generated purchase order. Represented as the UNIX epoch time. |
| destinationId | yes | string | Dwolla ID of the account receiving the funds. Format will always match `812-xxx-xxxx`. |
| amount | yes | string | Amount of order. Must be at least $0.01. |
| name | yes | string | Name of the item (100 character limit) |
| description | yes | string | Description of the item (200 character limit) |
| callback | no | string | URL to POST the transaction response to after the user authorizes the purchase. If not provided, will default to the Payment Callback URL set for the consumer application. If a URL could not be found in the application settings and this parameter is not set, a postback error will be induced. Only applies if `checkoutWithApi` is false (Pay Later flow). |
| redirect | no | string | URL to return the user to after they authorize or cancel a purchase. If not provided, the URL will default to the Payment Redirect URL set in the application's settings. If no URL could be found, an error will be induced. |
| allowFundingSources | no | string | Defaults to `false`, set to `true` to enable guest checkout and bank-funded payments. This would allow users without a Dwolla account to pass through the checkout flow. |
| checkoutWithApi | no | string | Defaults to `false` (and the Pay Now flow), set to `true` to use the Pay Later flow. |
| orderId | no | string | Optional string to identify the transaction (255 character limit). |
| test | no | string | Defaults to `false`, set to `true` to flag the PO for testing. Does not affect account balances nor are any e-mails sent. The transaction ID returned from a PO with `test` flagged will always be `1`. |
| tax | no | string | Tax applied to the order. Must be $0 or greater. |
| shipping | no | string | Shipping total for the order. Must be $0.00 or more. |
| facilitatorAmount | no | string | Amount of the facilitator fee (will override the amount in the application's settings). If set to `0`, the fee will be disabled. Must not exceed 25% of the transaction total. |
| notes | no | string | Note to attach to transaction (250 character limit). |

#### HMAC-SHA1 Hashing - Required
The hexadecimal hash is generated from the ampersand delimited text of the consumer key, [Unix timestamp](http://en.wikipedia.org/wiki/Unix_time), and orderId, even if an order ID is not provided. The text is then hashed using the consumer secret for the application as the hash key. The signature must be unique per request, so a new timestamp will need to be provided for each request. Below are examples on how to generate HMAC-SHA1 hashes in languages commonly used with web-stack development:

```raw
not applicable
``` 
```python
def sign_request():
  from hashlib import sha1
  import hmac
  import binascii
  import time

  key       = 'YOUR_APPLICATION_KEY'
  secret    = 'YOUR_APPLICATION_SECRET'
  timestamp = int(time.time())
  order_id  = 1
  text      = key + '&' + timestamp + '&' + order_id

  hash = hmac.new(secret, text, sha1)

  # We format the string to exclude the last character which is a newline delimiter.
  return binascii.b2a_base64(hashed.digest())[:-1]
```
```javascript
var crypto    = require('crypto')
  , key       = 'abcdeg'
  , secret    = 'YOUR_APPLICAITON_SECRET'
  , order_id  = 1
  , timestamp = (new Date).getTime()
  , text      = key + '&' + timestamp + '&' + order_id
  , hash;

hash = crypto.createHmac('sha1', secret).update(text).digest('hex');
```
```ruby
require 'rubygems'
require 'base64'
require 'cgi'
require 'hmac-sha1'

key       = 'YOUR_APPLICATION_KEY'
secret    = 'YOUR_APPLICATION_SECRET'
timestamp = Time.now.to_i
order_id  = 1

signature = "#{key}&#{timestamp}&#{order_id}"

hmac = HMAC::SHA1.new(secret)
hmac.update(signature)
hash = CGI.escape(Base64.encode64("#{hmac.digest}\n"))
```
```php
<?php
$key        = "YOUR_APPLICATION_KEY";
$secret     = "YOUR_APPLICATION_SECRET";
$timestamp  = time(); // example: 1390408833
$order_id   = 1;

$signature  = hash_hmac('sha1', "{$key}&{$timestamp}&{$order_id}", $secret);
?>
```

#### Example form
```html
<form accept-charset="UTF-8" action="https://www.dwolla.com/payment/pay" method="post">

<input id="key" name="key" type="hidden" value="abcdefg" />
<input id="signature" name="signature" type="hidden" value="abcd" />
<input id="timestamp" name="timestamp" type="hidden" value="1323302400" />
<input id="callback" name="callback" type="hidden" value="http://www.mywebsite.com/callback.aspx" />
<input id="redirect" name="redirect" type="hidden" value="http://www.mywebsite.com/redirect.aspx" />
<input id="test" name="test" type="hidden" value="false" />
<input id="name" name="name" type="hidden" value="Purchase" />
<input id="description" name="description" type="hidden" value="Description" />
<input id="destinationid" name="destinationid" type="hidden" value="812-713-9234" />
<input id="amount" name="amount" type="hidden" value="1.00" />
<input id="shipping" name="shipping" type="hidden" value="0.00" />
<input id="tax" name="tax" type="hidden" value="0.00" />
<input id="orderid" name="orderid" type="hidden" value="188375" />

<button type="submit">Submit Order</button>
</form>
```
Depending on the checkout flow utilized, the user will be redirected to the `redirect` URL with either completed transaction details or a confirmation page on the merchant's webpage wherein they will complete their transaction.

If any errors incur, they will be passed as parameters to the redirect URL:
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

#### Example return

```phpnoselect
<?php
// First, check for any errors
if (array_key_exists("error", $_GET)) {
    // find out what happened:
    $error_description = $_GET['error_description']; // "User Cancelled"
    exit($error_description);
}

// Create a function that verifies offsite-gateway signature hash against server-provided hash.
function verifyGatewaySignature($proposedSignature, $checkoutId, $amount) {
    global $secret;
    $key        = "Your key here";
    $secret     = "Your secret here";
    $amount = number_format($amount, 2);
    $signature = hash_hmac("sha1", "{$checkoutId}&{$amount}", $secret);
    return $signature == $proposedSignature;
}

// Second, extract the checkoutId, amount, and signature of the checkout:
$checkoutId = $_GET['checkoutId'];
$amount = $_GET['amount'];
$signature = $_GET['signature'];

// Third, validate the signature before you do anything with the data.
$signatureValid = verifyGatewaySignature($signature, $checkoutId, $amount);
if (!$signatureValid) {
    exit("Signature is not valid!");
}

$status = $_GET['status'];
if ($status == "Completed") {
    // do something useful with the checkout results:
    echo $status;
}

?>
```

<nav class="pager-nav">
    <a href="./">Back: Overview</a>
    <a href="" style="display:none;"></a>
</nav>
