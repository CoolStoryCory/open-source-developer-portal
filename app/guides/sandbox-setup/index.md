---
layout: twoColumn
section: guides
type: guide
guide: 
    name: sandbox-setup
    step: overview
title:  Get started with integrating bank transfers into your application
description: Test programmatic bank transfers with Dwolla's bank transfer API in our developer sandbox. 
---

# Getting started in Sandbox

## Sandbox environment

The Sandbox environment is a complete replica of the Dwolla production environment, supporting all of the same API endpoints. Applications should be tested against the Sandbox environment before being used in production.

#### Differences from production environment

- The Sandbox contains only test data and is completely separate from your production account
- Actual money is not sent or received as part of test transactions. Real financial data should never be used in the Sandbox
- All Access API endpoints have a base URL of `https://api-sandbox.dwolla.com/` instead of `https://api.dwolla.com/`

#### Transfer behavior in the Sandbox

Unlike balance sourced transfers, which are processed instantaneously, bank-sourced transfers exist in the pending state for a few business days until they are `processed`, `failed`, `cancelled`, or `reclaimed`.

The Sandbox environment does not replicate any ACH processes, so a `pending` transfer will not clear or fail automatically after a few business days as it would in production. It will simply remain in the `pending` state indefinitely. Reference our [testing resource article](/resources/testing.html) for more information on how-to simulate bank transfer processing in the Sandbox environment.

## Sandbox setup

During Sandbox account creation, Dwolla will associate a funding source, add $5000 to the account balance, and create an application. Once your account is created, you'll be redirected to our Sandbox Dashboard at `https://dashboard-sandbox.dwolla.com/` where you can view your API key and secret and [generate an OAuth access token](/resources/token-generator.html).

<a href="https://accounts-sandbox.dwolla.com" target="_blank" class="btn secondary large">Create your Sandbox account</a>

You’re ready to start sending money in the Sandbox! To simplify development, learn about configuring and using our [SDKs](/pages/sdks.html). You can jump straight into the [API docs](https://docsv2.dwolla.com/) or continue to the next guide below.

<nav class="pager-nav">
<a href="" style="display:none;"></a>
<a href="/guides/send-money">Next guide: Send money to your users</a>
</nav>
