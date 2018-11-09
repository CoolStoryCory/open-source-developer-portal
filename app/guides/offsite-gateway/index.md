---
layout: twoColumn
section: guides
type: guide
guide: 
    name: offsite-gateway
    step: overview
title: Off-Site Gateway
description: Utlize Dwolla's Off-Site Gateway to obtain an OAuth access token, used to access data in the Dwolla API on behalf of a user or application.
---

# Off-Site Gateway Checkout

<ol class="alerts">
    <li class="alert icon-alert-info">Dwolla's Off-Site Gateway is only available in API v1. This guide, along with our <a href="https://docs.dwolla.com/#checkouts">API v1 docs</a>, can be used as a reference to support existing Off-Site Gateway integrations. <strong>Note:</strong> The v1 API will continue to function for the foreseeable future, but Dwolla is no longer adding features, supporting minor bug fixes, or maintaining its SDKs.</li>
</ol>

## Overview

The [Off-Site Gateway API](https://docs.dwolla.com/#checkouts) is a way to seamlessly pay for goods or services online utilizing Dwolla's v1 API. Using a Dwolla-provided checkout experience, customers can authenticate and authorize payment of a purchase order utilizing Dwolla as the processing platform. Currently, there are two available checkout flows which provide (slightly) different user experiences, the pay now flow, or the pay later flow. For the purpose of this guide we will only cover the pay now flow, which is a checkout flow that can be initiated via one of the follow methods:

* [Direct submission](/guides/offsite-gateway/direct-submit.html): Submit a form that contains the checkout details (products, amount, total, etc) directly from the client to the Dwolla server.
* [Server-to server submission](/guides/offsite-gateway/server-to-server.html): Create a checkout session by communicating the checkout information from your server to the Dwolla server directly.

### User experience

Unlike the Pay Later flow, the checkout process is completed when the user clicks "Submit Payment" on the gateway and the payment is sent immediately to the merchant. The pay now flow does not require any action from the user after they are returned from the gateway to the merchant site.

1. User is prompted to create a Dwolla Direct account (If `allowGuestCheckout` and `AllowFundingSources` were enabled in the request), or they will log in using existing account.
![Screenshot of gateway Direct account creation](/images/gateway/createDirect.png "Dwolla Direct account creation")
  * If the user is already has a Dwolla account, they will be prompted to log in providing their email and password. Once the user successfully authenticates, they will be presented with the purchase order provided during the request.
![Screenshot of gateway login](/images/gateway/login.png "Off-site gateway login")
  * If the user clicks the "Cancel" button rather than logging in, they're redirected to the redirect URL with an error. If no redirect parameter was provided and no default Payment Redirect URL was set for the consumer application, they're redirected to dwolla.com.
2. User checks out/places order.
![Screenshot of gateway checkout](/images/gateway/checkout.png "Off-site gateway checkout")
  * The user will then need to provide their PIN number and click the "Submit" button to complete the checkout. The payment is sent immediately to the merchant. If there are any errors, the payment will cancel and no funds will be transferred.
3. Transaction completion, server callback, and user redirect.
![Screenshot of gateway paynow complete page](/images/gateway/paynow_complete.png "Off-Site Gateway paynow complete")

If the callback URL was provided during the request, the transaction details will be POSTed to the URL in a JSON-encoded string (As described in submission methods). This is completely transparent to the customer.

* * *

## Choose an integration option

<br>
<nav class="decision-nav">
    <div>
        <a href="direct-submit.html">
            Direct submit
        </a>
        <p>Form submit checkout details directly from the client to the Dwolla server.</p>
    </div>
    <div>
        <a href="server-to-server.html">
            Server-to-server
        </a>
        <p>Send checkout details from your server to the Dwolla server directly.</p>
    </div>
</nav>
