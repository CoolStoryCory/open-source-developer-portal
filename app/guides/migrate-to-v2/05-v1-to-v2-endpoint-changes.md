---
layout: twoColumn
section: guides
type: guide
guide: 
    name: migrate-to-v2
    step: '5'
title: Migrate to Access API | v1 to v2 Endpoint Changes
description: Underlying principles of Dwolla's Access API and guidance on upgrading your application from Dwolla's legacy v1 API. 
---

# V1 to Access API endpoint changes

The table below provides a mapping of endpoints in v1 to their equivalent in the Access API. Links to the Access API reference docs are provided alongside each method. Not all Access API endpoints are listed, therefore you’ll want to reference the API docs for new functionality such as [Customers](https://docsv2.dwolla.com/#customers) and [Webhook-Subscriptions](https://docsv2.dwolla.com/#webhook-subscriptions).  

| v1 name/link | v1 Endpoint (https://www.dwolla.com/oauth/rest) | Access API name/link | Access API Endpoint (https://api.dwolla.com) | Description |
|-----------|----------|----------------|-------------|----------------------|
| [Catalog](https://docs.dwolla.com/#catalog) | /catalog | [Root](https://docsv2.dwolla.com/#root) | / | Fetch a list of resources available to the user account. Entry point to the API. |
| [Send Money](https://docs.dwolla.com/#send-money) | /transactions/send | [Transfers - Initiate a transfer](https://docsv2.dwolla.com/#initiate-a-transfer) | /transfers | Send money from the authorized user to an email address or another Dwolla account. **Not supported:** Notes cannot be attached to transactions. Phone is not a supported destination type. FiSync is not currently a supported processing type. **New:** A Dwolla PIN is no longer needed to send money. |
| [Checkouts - Create a Checkout](https://docs.dwolla.com/#create-a-checkout) | /offsitegateway/checkouts | N/A | N/A | Off-site gateway is not currently supported in the Access API. Your application must continue to leverage v1 until further notice. |
| [Funding Sources - List Funding Sources](https://docs.dwolla.com/#list-funding-sources) | /fundingsources/ | [Accounts - List funding sources for an account](https://docsv2.dwolla.com/#list-funding-sources-for-an-account) | /accounts/{id}/funding-sources | List funding sources attached to a Dwolla account. |
| [Funding Sources - Get a Funding Source](https://docs.dwolla.com/#get-a-funding-source) | /fundingsources/{id} | [Funding sources - Retrieve a funding source](https://docsv2.dwolla.com/#retrieve-a-funding-source) | /funding-sources/{id} | Retrieve a funding source(either Bank Account or Dwolla balance) attached to a Dwolla account. |
| [Funding Sources - Withdraw to a funding source](https://docs.dwolla.com/#withdraw-to-a-funding-source) | /fundingsources/{id}/withdraw | [Transfers - Initiate a transfer](https://docsv2.dwolla.com/#initiate-a-transfer) | /transfers | Transfer money from a Dwolla balance to the bank of an authorized user. **New:** A Dwolla PIN is no longer needed to transfer funds. Webhooks are sent when moving money out of a balance. |
| [Funding Sources - Add money from a Funding Source](https://docs.dwolla.com/#add-money-from-a-funding-source) | /fundingsources/{id}/deposit | [Transfers - Initiate a transfer](https://docsv2.dwolla.com/#initiate-a-transfer) | /transfers | Transfer money from a bank to the Dwolla balance of an authorized user. **New:** A Dwolla PIN is no longer needed to transfer funds. Webhooks are sent when moving money into a balance. |
| [Funding Sources - Add new Funding Source](https://docs.dwolla.com/#add-new-funding-source) | /fundingsources/ | [Accounts - Create a funding source for an account](https://docsv2.dwolla.com/#create-a-funding-source-for-an-account) | /funding-sources | Attach a bank to a Dwolla account. The funding source will be added as unverified within Dwolla and will need to be verified before it’s eligible to send. **New:** A webhook is sent when a funding source is added. |
| [Funding Sources - Verify a Funding Sources](https://docs.dwolla.com/#verify-a-funding-source) | fundingsources/{id}/verify | Funding sources - [Initiate micro-deposits & Verify micro-deposits](https://docsv2.dwolla.com/#initiate-micro-deposits) | /funding-sources/{id}/micro-deposits | Verify micro-deposits initiated to a funding source belonging to an account. **New:** Webhooks are sent through the micro-deposit verification process. |
| [Transactions - List a user’s transactions](https://docs.dwolla.com/#list-a-user39s-transactions) | /transactions | [Accounts - List and search transfers for an account](https://docsv2.dwolla.com/#list-and-search-transfers-for-an-account) | /accounts/{id}/transfers | List all transactions for a Dwolla account. **Not supported:** Transaction types(money_sent, fee, withdraw, etc.) aren’t returned on transactions. |
| [Transactions - List an app’s transactions](https://docs.dwolla.com/#list-an-app39s-transactions) | /transactions | N/A | N/A | **Not supported:** The Access API does not have a method that allows an application to view transactions facilitated by the app itself. |
| [Transactions - Get a specific transaction](https://docs.dwolla.com/#get-a-specific-transaction) | /transactions/{id} | [Transfers - Retrieve a transfer](https://docsv2.dwolla.com/#retrieve-a-transfer) | /transfers/{id} | Retrieve a transfer belonging to the authorized user. **Not supported:** Transaction type is not returned on the transfer. Expected clearing date is no longer returned for bank transfers. Source and destination names are no longer displayed on the transfer object. |
| [Transactions - Search transactions](https://docs.dwolla.com/#search-transactions) | /transactions/search | [Accounts - List and search transfers for an account](https://docsv2.dwolla.com/#list-and-search-transfers-for-an-account) | /accounts/{id}/transfers | Search transactions for the authorized user. **New:** Transaction search is included on the List Transfers for an Account endpoint. Same request params supported on the Access API as v1. |
| [Scheduled Payments](https://docs.dwolla.com/#scheduled-payments) | /transactions/scheduled | N/A | N/A | **Not supported:** Includes: Create, List, Retrieve, Edit, Delete, and Delete all Scheduled Payments. **Note:** Scheduled transactions are allowed in the Access API, however you’ll need to build your own scheduler and call the transfers endpoint. |
| [Transaction Stats](https://docs.dwolla.com/#transaction-stats) | /transactions/stats | N/A | N/A | **Not supported.** Information will likely be accessible in the new Dwolla dashboard. |
| [Users - Get Basic Account Info](https://docs.dwolla.com/#get-basic-account-info) | /users/{account_identifier} | [Accounts - Retrieve account details](https://docsv2.dwolla.com/#retrieve-account-details) | /accounts/{id} | Retrieve name and unique account identifier for a Dwolla account. |
| [Users - Get Full Account Info](https://docs.dwolla.com/#get-full-account-info) | /users | N/A | N/A | **Not supported** |
| [Users - Get Email Address](https://docs.dwolla.com/#get-email-address) | /users/email | N/A | N/A | **Not supported** |
| [Users - Get Avatar](https://docs.dwolla.com/#get-avatar) | /avatars/{dwolla_id} | N/A | N/A | **Not supported** |
| [Users - Find Nearby Businesses](https://docs.dwolla.com/#find-nearby-businesses) | /contacts/nearby | N/A | N/A | **Not supported** |
| [Users - Find Users Nearby](https://docs.dwolla.com/#find-users-nearby) | /users/nearby | N/A | N/A | **Not supported** |
| [Contacts - Get a user’s contacts](https://docs.dwolla.com/#get-a-user39s-contacts) | /contacts | N/A | N/A | **Not supported** |
| [Balance](https://docs.dwolla.com/#balance) | /balance | [Funding sources - Retrieve a funding source](https://docsv2.dwolla.com/#retrieve-a-funding-source) | /funding-sources/{id} | Retrieve the balance of a funding source. |
| [MassPay - Create Job](https://docs.dwolla.com/#create-job) | /masspay | [MassPay - Initiate a mass payment](https://docsv2.dwolla.com/#initiate-a-mass-payment) | /mass-payments | Initiate a mass payment for the authorized account to many recipients. **New:** Metadata can be added to a MassPay job. Webhooks are sent notifying your app of the status of a mass payment. |
| [MassPay - List Jobs](https://docs.dwolla.com/#list-jobs) | /masspay | [Accounts - List mass payments for an account](https://docsv2.dwolla.com/#list-mass-payments-for-an-account) | /accounts/{id}/mass-payments | Retrieve all mass payments for the authorized account. |
| [MassPay - Retrieve Job](https://docs.dwolla.com/#retrieve-job) | /masspay/{id} | [MassPay - Retrieve a mass payment](https://docsv2.dwolla.com/#retrieve-a-mass-payment) | /mass-payments/{id} | Retrieve a single mass payment for the authorized account. |
| [MassPay - List a Job’s Items](https://docs.dwolla.com/#list-a-job39s-items) | /masspay/{id}/items | [MassPay - List items for a mass payment](https://docsv2.dwolla.com/#list-items-for-a-mass-payment) | /mass-payments/{id}/items | List the items contained in a mass payment. |
| [MassPay - Retrieve a Job’s Item](https://docs.dwolla.com/#retrieve-a-job39s-item) | /masspay/{job_id}/items/{item_id} | [MassPay - Retrieve a mass payment item](https://docsv2.dwolla.com/#retrieve-a-mass-payment-item) | /mass-payment-items/{id} | Retrieve a single item for a mass payment. |
| [Refund](https://docs.dwolla.com/#refund) | /transactions/refund | N/A | N/A | **Not supported.** Your app will need to handle refunds by calling the [initiate a transfer](https://docsv2.dwolla.com/#initiate-a-transfer) endpoint, specifying the source, destination and amount to be refunded. |
| [Money Requests](https://docs.dwolla.com/#money-requests) | /requests | N/A | N/A | Includes: Create, List, Retrieve, Fulfill, and Cancel a Money Request. |
| [Account Settings - Get Auto Withdrawal Status](https://docs.dwolla.com/#get-auto-withdrawal-status) | /accounts/features/auto_withdrawl | N/A | N/A | **Not supported** |
| [Account Settings - Enable/Disable Auto Withdrawal](https://docs.dwolla.com/#enabledisable-auto-withdrawal) | /accounts/features/auto_withdrawl | N/A | N/A | **Not supported** |

<nav class="pager-nav">
    <a href="04-sdk-support.html">Back: SDK support</a>
    <a href="06-other-notable-changes.html">Next step: Other notable changes</a>
</nav>
