---
layout: twoColumn
section: Dwolla.js
type: article
title:  "Instant account verification"
weight: 1
description: "Integrate instant bank verification for developers using the Dwolla ACH API, allowing customers to add their bank account quickly."
---

# Dwolla.js

## Using Dwolla.js for Instant Account Verification (IAV)
For Access API partners, `dwolla.js` has the added function of facilitating Instant Account Verification (IAV) on their customer's bank or credit union account. By calling a separate function `dwolla.iav.start()`, the Access API partner application can render the IAV flow within a specified container. `dwolla.iav.start()` allows for customization through configurable options such as: 

* `stylesheets` - a list of CSS stylesheets for styling the IAV flow 
* `microDeposits` - presents a selection screen for the user to choose the micro-deposit method of bank verification throughout the IAV flow
* `fallbackToMicroDeposits` - presents a selection screen for the user to fallback to selecting the micro-deposit method of bank verification within the IAV flow
* `backButton` - displays a back button throughout the IAV flow
* `subscriber` - a function that can be used by an application to subscribe to state changes throughout the IAV flow

### dwolla.iav.start()

Parameter | Type | Value
----------|-------------|--------------
iav-token | string | A single use IAV token [generated on your server](https://docsv2.dwolla.com/#generate-an-iav-token).
options | object | An object containing configurable options. Contains keys: `container`, `stylesheets`, `microDeposits`, `fallbackToMicroDeposits`, `backButton`, and `subscriber`. *See example below*. <br> `container` represents a string value container element where IAV will render. <br> `stylesheets` represents an array list of stylesheets to load IAV styles. <br> `microDeposits` represents a boolean *true* or *false* value which determines if the micro-deposit method of bank verification is presented as an option throughout the IAV flow. <br> `fallbackToMicroDeposits` represents a boolean *true* or *false* value which determines if a fallback selection screen appears for choosing an alternative bank verification method. <br> `backButton` represents a boolean *true* or *false* value which determines if a back button is displayed throughout the IAV flow. <br> `subscriber` is a function that can be used to subscribe to state changes throughout the IAV flow. This function will be called with an object containing a `currentPage` and an optional `error` attribute.
callback | function | A callback function that handles the response from Dwolla.

#### Example

```javascriptnoselect
dwolla.iav.start('8zN400zyPUobbdmeNfhTGH2Jh5JkFREJa9YBI8SLXp0ERXNTMT', {
  container: 'iavContainer',
  stylesheets: [
    'https://fonts.googleapis.com/css?family=Lato&subset=latin,latin-ext',
    'https://myapp.com/iav/customStylesheet.css'
  ],
  microDeposits: false,
  fallbackToMicroDeposits: true,
  backButton: true,
  subscriber: ({ currentPage, error }) => {
      console.log('currentPage:', currentPage, 'error:', JSON.stringify(error))
    }
}, function(err, res) {
  console.log('Error: ' + JSON.stringify(err) + ' -- Response: ' + JSON.stringify(res));
});
```

## Handling IAV errors
IAV related errors are returned either as user-facing messages within the IAV flow, or triggered through the dwolla.js callback. User-facing messages are displayed in red text at the top of the bank login screen informing the user there was an issue with connecting the associated bank account. Error callbacks will trigger letting your app know of errors that can't be fixed by the user within the flow.

Error callbacks will contain a similar JSON error response body as [common errors](https://docsv2.dwolla.com/#errors) in the API, which includes a top-level error `code` and a `message`. Reference the following list of error callbacks:

| Code | Message | Description |
|--------------|-------------|--------------------|
| UnexpectedPage | IAV navigated to an unexpected page and was cancelled. | A Dwolla related error occurred. |
| InvalidIavToken | Invalid IAV token. | An IAV token has already been used or has exceeded its expiration time of 1 hour. |
| UnsupportedBank | Sorry, that financial institution is not supported. If possible, please choose a different one or an alternative method for connecting your financial institution. | The customer's bank is not supported by the IAV flow. |
| RateLimitReached | Sorry, we’re having trouble logging into your account. Please try a different account. | The customer exceeded the max number of two IAV attempts with the same bank. The customer must wait 30 minutes in order to re-authenticate.  |

##### **Example error callback**

```noselect
{
 "code": "RateLimitReached",
 "message": "Sorry, we’re having trouble logging into your account. Please try a different account."
}
```

### Testing IAV success and error scenarios in Sandbox
Dwolla's Sandbox environment uses a fake service which allows you to simulate the end-user experience for adding and verifying a bank account within the IAV flow. The default "success" scenario is a happy path flow that includes: a single set of MFA questions, presentation of two valid accounts and one account without a routing number. Any text can be entered in the input fields to allow you to proceed through the IAV flow.

To help you test error scenarios, a flag and an option can be used in the first field (usually username or ID) for the bank you are trying authenticate. The remaining input fields in the flow can be any string of text, as we allow you to proceed through the flow regardless of the information entered. Use the following flag and option values to test various error scenarios:

|     Flag      |   Option     | User-facing message |
|:------------- |:----------------- |----------------|-------------|
| -e  | InvalidLogin | Please make sure your login or security information is correct. | 
| -e  | AccountNotFound | Sorry, we’re unable to find your {} account. Please try again or use a different account. | 
| -e  | UnsupportedSite | Sorry, that financial institution is not supported. If possible, please choose a different one or an alternative method for connecting your financial institution. | 
| -e  | AlreadyLoggedIn | If you're currently logged in to your {} account, please log out and try again. | 
| -e  | VisitSite | We’re unable to process your information because the {} site is currently requiring additional action from you. Please resolve this, then try again. | 
| -e  | UnavailableSite | Sorry, there seem to be some technical difficulties while attempting to process your information. Please try again later. | 

#### **Example error scenario**
![Screenshot of IAV user facing error message](/images/IAVErrorMessage.png "IAV user facing error")


## Options and customization
#### `microDeposits`
Your application can present the micro-deposit method of bank verification throughout the IAV flow by setting the `microDeposits` option to *true*. This option gives the user the ability to initially select either the micro-deposit method of bank verification or IAV, as well as fallback to selecting the micro-deposit method of bank verification if un-successful connecting a bank through the IAV flow. 

![Screenshot of micro-deposit fallback](/images/microdeposits-fallback.png "fallback to micro-deposits")

#### `fallbackToMicroDeposits`
If your application sets the `fallbackToMicroDeposits` option to *true*, a fallback selection screen will appear after **two** failed attempts if there was an issue with connecting a bank using IAV. This selection screen asks the user to choose from either the traditional micro-deposit method of bank verification or re-attempt the IAV flow choosing a different financial institution.

#### `backButton`
By default, a back button will **not** be displayed throughout the IAV flow. If your application sets the `backButton` option to *true*, a back button will appear in the lower left corner of the container throughout the IAV flow.

#### `subscriber`
An optional `subscriber` function can be used if your application is wanting to receive additional information on where the user is within the IAV flow, as well as if a user-facing message was presented to the user. `subscriber` is a function that will be called with an object containing a `currentPage` and an optional `error` attribute. `currentPage` will be a string value that identifies what page the user is on in the IAV flow. If an error occurs, the `error` attribute will be returned along with `currentPage`. `error` will be a JSON object, which includes a top-level error `code` and a `message` (similar to [common errors](https://docsv2.dwolla.com/#errors) in the API). Reference the following table for possible values:

|     Attribute     | Value |
|:-------------|-------------|
| currentPage | `BankSearch` <br> `BankLogin` <br> `MFA` <br> `SelectAccount` <br> `SuccessIAV` <br> `SelectVerificationMethod` <br> `SubmitBankAccountDetails` <br> `SuccessMicroDeposits` | 
| error | **code** and **message**: <br> `InvalidLogin` - "Please make sure your login or security information is correct." <br> `AccountNotFound` - "Sorry, we’re unable to find your {} account. Please try again or use a different account." <br> `UnsupportedSite` - "Sorry, that financial institution is not supported. If possible, please choose a different one or an alternative method for connecting your financial institution." <br> `AlreadyLoggedIn` - "If you're currently logged in to your {} account, please log out and try again." <br> `VisitSite` - "We’re unable to process your information because the {} site is currently requiring additional action from you. Please resolve this, then try again." <br> `UnavailableSite` - "Sorry, there seem to be some technical difficulties while attempting to process your information. Please try again later." | 

##### **Example subscriber callback**

```noselect
{
  "currentPage":"BankLogin",
  "error":{
    "code":"InvalidLogin",
    "message":"Please make sure your login or security information is correct."
  }
}
```

#### `stylesheets`
Dwolla provides a list of CSS classes available for styling certain elements of the IAV flow. These elements can be easily customized to match the look and feel of your application, and are included within the `options` JavaScript object of the function dwolla.iav.start(). You can specify one or many stylesheets as a list within the `stylesheets` attribute. By default, the elements within your specified container are responsive to any change in screen size.

<a href="https://www.dwolla.com/dwollajs-bank-verification" target="_blank" class="btn secondary medium">Demo Dwolla.js</a>

#### List of CSS classes
```cssnoselect
/* Available css classes for customization*/
.dwolla-iav-text-box,
.dwolla-iav-button,
.dwolla-iav-header,
.dwolla-iav-text,
.dwolla-iav-link,
.dwolla-iav-error,
.dwolla-iav-label,
.dwolla-iav-header,
.dwolla-iav-text-box,
.dwolla-iav-text-box:focus,
.dwolla-iav-link,
.dwolla-iav-link:hover,
.dwolla-iav-button,
.dwolla-iav-button:hover,
.dwolla-iav-button:active,
.dwolla-iav-check-image,
.dwolla-iav-radio-hover,
.dwolla-iav-radio-selected
```

* * *

#### View:

*   [Funding source verification - IAV](/resources/funding-source-verification/instant-account-verification.html)
*   [Dwolla.js - Overview](/resources/dwolla-js.html)
*   [Add a bank account](/resources/dwolla-js/add-a-bank-account.html)
*   [On-demand bank transfers](/resources/dwolla-js/on-demand-bank-transfers.html)
