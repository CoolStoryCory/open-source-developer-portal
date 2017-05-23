---
layout: twoColumn
section: guides
type: guide
guide: 
    name: auth
    step: '1'
title: Dwolla OAuth 2.0 | Authorization Code Flow
description:  Obtain an OAuth access token, used to access data in the Dwolla API on behalf of a user or application.
---

# Overview - Obtaining an account access token

The [authorization code](https://tools.ietf.org/html/rfc6749#section-4.1) flow is the most common OAuth flow and is used when an application is wanting to obtain permission to act on behalf of a user. This is a redirection-based flow where the user (Resource owner) is sent from your application to Dwolla (typically in a web-browser) to authenticate and authorize your app. Once the user grants authorization, Dwolla (the Authorization Server) will redirect them back to your application where you'll exchange a temporary authorization code for an account `access_token` and `refresh_token` pair. An account access token can then be used to make calls  like transfer money, add funding sources, and more to the Dwolla API on behalf of the user. 

**OAuth account creation:** If the user does not yet have a Dwolla account they are given the option to sign up before granting permission using Dwolla's [OAuth account creation](/guides/auth/oauth-account-creation.html) experience. 

> **Important:** Dwolla’s implementation of the OAuth 2.0 standard uses short-lived access tokens and long-lived refresh tokens.
> A refresh token is paired with an access token and can be used within 60 days to generate a new access token and refresh token pair. 
> Learn more on how to refresh authorization towards the end of this guide. 

#### Scopes
Before sending a user through the OAuth flow, you must explicitly specify which access scopes you will require. This helps users better understand what kind of data your application wishes to access, and which permissions they are granting your application. Multiple scopes may be included in your authorization request using the pipe ("|") character (e.g. scope=send|transactions).

Requested scopes must also be enabled on your application settings, otherwise Dwolla will respond with an error stating that your application requested invalid scopes.

| Scope Name | Description |
|-----------|--------------|
| Transactions | Access the user's transfer data. |
| Send | Transfer money on the user's behalf. |
| Funding | Access names of funding sources the user has connected to Dwolla, access available balance information for Dwolla Balance, add new funding sources, verify funding sources, initiate transfers to and from funding sources. |
| AccountInfoFull | Includes name and Dwolla account ID |

* * *

## Step 1: Request user authorization
Generate a URL which describes the permissions your application requires (`scope`), who the client application is (`client_id`), and where the user should be redirected to after they grant or deny permissions to your application (`redirect_uri`). Optionally, specify if you want the user to create a [full Dwolla account](/resources/account-types/transfer-accounts.html) using the `verified_account` querystring parameter, and whether you want to initially present either the create an account or login screen using the `dwolla_landing` parameter.

#### Request format

```noselect
Production:
https://www.dwolla.com/oauth/v2/authenticate?client_id={client_id}&response_type=code&redirect_uri={redirect_uri}&scope={scope}

Sandox:
https://sandbox.dwolla.com/oauth/v2/authenticate?client_id={client_id}&response_type=code&redirect_uri={redirect_uri}&scope={scope}
```

> Remember to [url-encode](http://en.wikipedia.org/wiki/Percent-encoding) all GET querystring parameters!

##### Request parameters
| Parameter | Required | Type | Description |
|-----------|----------|----------------|-------------|
| client_id | yes | string | Application key. Navigate to `https://www.dwolla.com/applications` (production) or `https://dashboard-sandbox.dwolla.com/applications` (Sandbox) for your application key. |
| response_type | yes | string | This must always be set to `code`. |
| redirect_uri | yes | string | URL where the user will be redirected to afterwards. The value of this parameter must match one of the values that appear in your [application details](https://www.dwolla.com/applications) page. (We compare: protocol, subdomain, domain, tld, and file path. Querystring parameters are ignored) |
| scope | yes | string | Permissions you are requesting.  See [below](#oauth-scopes) for list of available scopes.  Scopes are delimited by a pipe ("&#124;") |
| state | no | string | Primary purpose is protection against cross-site request forgery (CSRF) attacks. A random value set by the client (you) in the initial OAuth authorization request, and is then made available to you in the redirect (along with the authorization `code`).  |
| verified_account | no | string | Require new users opting to register for Dwolla to create a fully-verified Dwolla account instead of a default lightweight Direct account. |
| dwolla_landing | no | string | An optional override that force displays either the login or create an account screen. Possible values are: `login`, `register`, or `null`. |

#### Example request
```noselect
https://sandbox.dwolla.com/oauth/v2/authenticate?client_id=JCGQXLrlfuOqdUYdTcLz3rBiCZQDRvdWIUPkwssGMuGhkem9Bo&response_type=code&redirect_uri=https%3A%2F%2Fwww.myapplication.com%2Fredirect&scope=send%7Ctransactions&dwolla_landing=login&state=6f102687b52e4f09b5f3a1c2966f2f41
```

### Request user authorization errors
There are a few different errors that can occur in the initial OAuth authorization request that you will want to be prepared to handle. These errors will return a user facing message when they are redirected to Dwolla, notifying them that the application has an "Invalid client configuration".  

![Screenshot of Invalid OAuth config](/images/oauth/invalid-oauth-config.png "Invalid OAuth config")

Since this is a generic error, your app will want to check all values specified in the initial authorization request. When checking the values in your request, here are a few things to keep in mind:

* client id: Does the `client_id` belong to a valid application created in the environment you are calling?
* code: Does the `response_type` parameter equal the string value `code`?
* redirect uri: Does the `redirect_uri` value match the value set in the OAuth Redirect URL field in your application's settings?
* scope: Are the scopes you are requesting enabled on your application?

## Step 2: Handle the OAuth redirect
Your `redirect_uri` specified in the request is used by Dwolla to handle both the approval of authorization, as well as if the user cancels out of the flow or denies authorization. If the user denies authorization an error will be returned as an `error` querystring parameter.

![Screenshot of permissions screen](/images/oauth-permissions-screen.png "OAuth permissions screen")

#### **User grants authorization**

If the user authorizes your application, Dwolla will take them back to the supplied `redirect_uri`, along with a temporary authorization `code` as a GET querystring parameter (and a state parameter, if one was specified in the initial authorization request). **Note:** This is not an access token, yet. You will need to exchange this authorization code for an actual account access token and refresh token pair in the next step.

*Approval example:* `https://www.myapplication.com/redirect?code=votwMtogKbmFxolxp9stBBnBvJVy`

#### **User denies authorization**

If the user selects “deny” on the permissions page, Dwolla will take them back to the supplied `redirect_uri`, along with an `error` and `error_description` querystring parameters.

*Rejection example:* `https://www.myapplication.com/redirect?error=access_denied&error_description=The+user+denied+the+request`

## Step 3: Finish user authorization
In the final step of the authorization code flow, you will exchange the code granted on step 2 for an OAuth access token and refresh token pair. In order to do so, you will need to make an HTTP request from your application server to the Dwolla authorization server. The temporary authorization code is a single-use code that should be exchanged within 60 seconds of being returned in the `code` querystring parameter.

##### HTTP request
`POST https://www.dwolla.com/oauth/v2/token`

Including the `Content-Type: application/x-www-form-urlencoded` header, the request is sent to the token endpoint with the following `form-encoded` parameters:

##### Request parameters
| Parameter | Required | Type | Description |
|-----------|----------|----------------|-------------|
| client_id | yes | string | Application key. Navigate to `https://www.dwolla.com/applications` (production) or `https://dashboard-sandbox.dwolla.com/applications` (Sandbox) for your application key. |
| client_secret | yes | string | Application secret. Navigate to `https://www.dwolla.com/applications` (production) or `https://dashboard-sandbox.dwolla.com/applications` (Sandbox) for your application secret. |
| code | yes | string | The authorization code included in the redirect URL. Single use `code` with an expiration of 60 seconds. |
| grant_type | yes | string | This must be set to `authorization_code`. |
| redirect_uri | yes | string | The same redirect_uri specified in the initiation step. |

#### Example request
```noselect
POST https://sandbox.dwolla.com/oauth/v2/token
Content-Type: application/x-www-form-urlencoded

client_id=CGQXLrlfuOqdUYdTcLz3rBiCZQDRvdWIUPkwasGMuGhkem9Bo&client_secret=g7QLwvO37aN2HoKx1amekWi8a2g7AIuPbD5CcJSLqXIcDOxfTr&code=h6TvQZHr5BsVcfO43uOJ0uRkBLki&grant_type=authorization_code&redirect_uri=https%3A%2F%2Fwww.myapplication.com%2Fredirect
```

### Example OAuth flow
Our [Ruby](https://github.com/Dwolla/dwolla-v2-ruby), [Python](https://github.com/Dwolla/dwolla-v2-python), and [Node.js](https://github.com/Dwolla/dwolla-v2-node) SDKs contain support for integrating the authorization code flow into your application, which is used to obtain an account access token. For languages where Dwolla does not have a supported SDK (e.g. PHP), we recommend using an external REST client. The following example shows a complete reference example of implementing the authorization code flow into your application.

```raw
not applicable
``` 
```python
import dwollav2

# Navigate to https://www.dwolla.com/applications (production) or https://dashboard-sandbox.dwolla.com/applications (Sandbox) for your application key and secret.
app_key = '...'
app_secret = '...'
client = dwollav2.Client(key = app_key,
                         secret = app_secret,
                         environment = 'sandbox') # optional - defaults to production

state = binascii.b2a_hex(os.urandom(15))
client.Auth(redirect_uri = 'https://yoursite.com/callback',
            scope = 'ManageCustomers|Funding',
            state = state)

# redirect the user to dwolla.com for authorization
redirect_to(auth.url)

# exchange the code for a token using the query params provided to the redirect_uri
token = auth.callback(req.GET)
```
```javascript
// where to send the user after they grant permission:
var redirect_uri = "https://www.myredirect.com/redirect";  

var auth = new client.Auth({
  redirect_uri: 'http://yoursite.com/callback',
  scope: 'ManageCustomers',
  state: getRandomHex(), // optional - https://tools.ietf.org/html/rfc6749#section-10.12
  verified_account: true, // optional
  dwolla_landing: 'register', // optional
});

// redirect to `auth.url`

auth.callback(req.query) // pass the code and optional state to the callback
  .then(function(token) {
    return token.get('/');
  })
  .then(function(res) {
    console.log(JSON.stringify(res.body));
  });
```
```ruby
# config/initializers/dwolla.rb
require 'dwolla_v2'

# Navigate to https://www.dwolla.com/applications (production) or https://dashboard-sandbox.dwolla.com/applications (Sandbox) for your application key and secret.
app_key = "..."
app_secret = "..."
$dwolla = DwollaV2::Client.new(key: app_key, secret: app_secret) do |config|
  config.environment = :sandbox # optional - defaults to production
end

# app/controllers/your_auth_controller.rb
class YourAuthController
  # redirect the user to dwolla.com/oauth/v2/authenticate
  def authenticate
    redirect_to auth.url
  end

  # exchange the code for a token
  def callback
    account_token = auth.callback(params)
  end

  private

  def auth
    $dwolla.auths.new redirect_uri: "https://www.myredirect.com/redirect",
                      scope: "send|funding",
                      state: session[:state] ||= SecureRandom.hex
  end
end
```
```php
/**
 *  No example for this language yet.
 **/
```

* * *

## Refreshing an account access token
Because access tokens are short-lived and expire 1 hour after being issued, if you wish to maintain authorization for a user account for a duration of time longer than 1 hour, you can use the refresh token to generate a new access token and refresh token. There is no limit to the number of times you can refresh a user's access token. Nonetheless, it is important to keep in mind that upon a successful refresh your existing access token will be invalidated.

**Note:** We recommend safely storing a user's refresh token, i.e. in a database, so you can use it in the future to [refresh authorization](https://docsv2.dwolla.com/transfer/#refresh-account-authorization). If you misplace or delete a refresh token, then you will need to repeat the OAuth flow starting from the first step described above.

##### HTTP request
`POST https://www.dwolla.com/oauth/v2/token`

Including the `Content-Type: application/x-www-form-urlencoded` header, the request is sent to the token endpoint with the following `form-encoded` parameters:

##### Request parameters
| Parameter | Required | Type | Description |
|-----------|----------|----------------|-------------|
| client_id | yes | string | Application key. Navigate to `https://www.dwolla.com/applications` (production) or `https://dashboard-sandbox.dwolla.com/applications` (Sandbox) for your application key. |
| client_secret | yes | string | Application secret. Navigate to `https://www.dwolla.com/applications` (production) or `https://dashboard-sandbox.dwolla.com/applications` (Sandbox) for your application secret. |
| refresh_token | yes | string | A valid refresh token. |
| grant_type | yes | string | This must be set to `refresh_token`. |

#### Example request

```raw
POST https://sandbox.dwolla.com/oauth/v2/token
Content-Type: application/x-www-form-urlencoded

client_id=CGQXLrlfuOqdUYdTcLz3rBiCZQDRvdWIUPkwasGMuGhkem9Bo&client_secret=g7QLwvO37aN2HoKx1amekWi8a2g7AIuPbD5CcJSLqXIcDOxfTr&grant_type=refresh_token&refresh_token=Pgkel9okjwTCfsvIvEDPrsomE1er1txeyoaAkTIBAuXza8WvZY
``` 
```python
# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python
# Takes a Token as its first argument and returns a new token set.
new_tokens = client.Auth.refresh(expired_token)
```
```javascript
// Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-node
var oldToken = new client.Token({ refresh_token: 'sHzEauv7FVpga2BSHVecKqFmCUfuhbBa4JRuClFuYa5vUSUdhL' });
client.auth.refresh(oldToken)
  .then(function(token) {
    return token.get('/');
  })
  .then(function(res) {
    console.log(JSON.stringify(res.body));
  });
```
```ruby
# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby
# Takes a Token as its first argument and returns a new token set.
new_tokens = $dwolla.auths.refresh(expired_token)
```
```php
/**
 *  No example for this language yet.
 **/
```

That's it! You're ready to start [making requests](/guides/auth/using-an-access-token.html) to the [Dwolla API](https://docsv2.dwolla.com/) on behalf of a user.

<nav class="pager-nav">
    <a href="./">Back: Overview</a>
    <a href="using-an-access-token.html">Next step: Using an access token</a>
</nav>
