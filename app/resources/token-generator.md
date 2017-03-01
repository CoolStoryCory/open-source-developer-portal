---
layout: twoColumn
section: resources
type: tool
title:  "Token Generator"
description: "Generate an OAuth access token for Dwolla's bank transfer API."
---

# Token generator

The Dwolla API requires an OAuth access token for authorization. Whether you’re leveraging Dwolla's Transfer solution for payouts or integrating with the Access API, sometimes you just need an access token for your own account without creating an entire OAuth flow.

### Generate an access token for your own Dwolla account

Navigate to the applications page, which contains a list of your created applications. For Production, visit [https://www.dwolla.com/applications](https://www.dwolla.com/applications).

<img src="/images/token-generator-prod.png" alt="Production Token Generator"/>

For Sandbox, visit [https://dashboard-uat.dwolla.com/applications](https://dashboard-uat.dwolla.com/applications).

<img src="/images/token-generator.png" alt="Sandbox Token Generator" style="width: 500px;"/>

Press the **create token** button located beneath an application to generate a token with the scopes enabled that application. 

**Important:** Keep in mind that Dwolla’s implementation of the [OAuth 2.0](https://tools.ietf.org/html/rfc6749) standard uses short-lived access tokens and long-lived refresh tokens. The access token will expire one hour after it’s generated and you will have to generate a new one. Eventually, you'll need to implement a refresh strategy. Reference the [OAuth refresh strategies](https://developers.dwolla.com/resources/oauth-refresh-strategies.html) article for more information.
