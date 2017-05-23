---
layout: twoColumn
section: guides
type: guide
guide: 
    name: migrate-to-v2
    step: '3'
title: Migrate to Access API | Authentication
description: Underlying principles of Dwolla's Access API and guidance on upgrading your application from Dwolla's legacy v1 API. 
---

# Authentication

The way in which you interact with Dwolla’s authorization server will remain **unchanged**. OAuth user account access tokens that are currently issued to applications leveraging v1 can be used to call various Access API endpoints. 

Scopes used in the Access API: `Send`, `Transactions`, `Funding`, `AccountInfoFull`, and `ManageCustomers`.

In v1, some API endpoints only require your application key and secret (for instance, creating a checkout or looking up a user), but most require an OAuth access token. In the Access API, all API endpoints require either an OAuth user **account access_token** or an **application access_token** in order to authenticate. Application access tokens are new and only used to access protected resources that belong to the application itself including: webhooks, webhook-subscriptions, and events.

For endpoints that require an OAuth access token, it should be included in the Authorization HTTP header like so: `Authorization: Bearer <TOKEN_HERE>`.

<nav class="pager-nav">
    <a href="02-making-requests.html">Back: Making requests</a>
    <a href="04-sdk-support.html">Next step: SDK support</a>
</nav>
