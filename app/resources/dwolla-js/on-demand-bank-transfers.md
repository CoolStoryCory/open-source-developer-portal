---
layout: twoColumn
section: Dwolla.js
type: article
title:  "On-demand bank transfers"
weight: 2
description: "Quickly integrate instant bank verification for developers using the Dwolla ACH API, allowing for easy use of on-demand bank transfers."
---

# Dwolla.js

## Using Dwolla.js for On-demand bank transfers
On-demand bank transfers allow Dwolla API partners to receive variable totals from a payer’s bank account via an ACH transaction—perfect for companies with usage-based business models, such as utilities or advertising platforms. No other third-party gateways or merchant accounts required, just your Dwolla integration and payer authorization.

This is an account level setting for an Dwolla API partner. When enabled, the end user is presented with text on the bank selection screen within the IAV flow giving authorization to Dwolla for future variable payments.

![Screenshot of On-demand](/images/OnDemandIAV.png "On-demand bank transfers")

Once you have collected all of the authorizations required for a bank transfer, including the additional authorization from the Customer for on-demand transfers, you will be able to <a href="https://docsv2.dwolla.com/#initiate-a-transfer">initiate transfers</a> for variable amounts and dates.

* * *

#### View:

*   [Dwolla.js - Overview](/resources/dwolla-js.html)
*   [Add a bank account](/resources/dwolla-js/add-a-bank-account.html)
*   [Instant account verification](/resources/dwolla-js/instant-account-verification.html)
