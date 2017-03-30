---
layout: twoColumn
section: guides
type: guide
guide: 
    name: auth
    step: '1a'
title: Dwolla OAuth 2.0
description: Obtain an OAuth access token, used to access data in the Dwolla API on behalf of a user or application. 
---

## Overview

Dwolla has simplified the user on-boarding process by building account creation into the OAuth flow. Through Dwolla's co-branded OAuth experience, your users can create a Dwolla account and attach a bank account inline with the capture of the user's permission, without abandoning your application flow.

There are three ways to onboard new users, each with varying levels of required verification:

* Create a Dwolla Direct account with unverified, receive-only `bank` funding source
* Create a Dwolla Direct account with instant bank account verification (IAV)
* Create a Full Dwolla account with both identity and bank verification

Dwolla Direct accounts were designed to provide a lightweight registration experience. In contrast to full Dwolla accounts, full identity verification is not required for Dwolla Direct accounts. Because of this, they have certain restrictions within the Dwolla platform, e.g. Dwolla Direct accounts cannot hold a Dwolla balance. Read more on the difference between Direct and full Dwolla accounts in our [account types](/resources/account-types/traditional-accounts.html) resource article.

## User experience - Dwolla Direct account

By default, Dwolla Direct account creation is enabled. If your application will be receiving payments from users or paying out to users, then Dwolla Direct is the recommended account type. Requested permissions (scopes) determine whether the Direct account will be prompted to add a verified bank or an unverified bank.

##### Example URL format:

`https://sandbox.dwolla.com/oauth/v2/authenticate?client_id={client_id}&response_type=code&redirect_uri={redirect_uri}&scope={scope}&dwolla_landing=register`

#### Step 1: Redirect the user to Dwolla
From your application, the user is presented with Dwolla OAuth in a pop-up, or their window is redirected. 

#### Step 2: User is prompted to create a Direct account
User is prompted to create a Dwolla Direct account (or log in with an existing Dwolla account). By default, if Dwolla detects no prior user sessions from dwolla.com, the main call to action will be to create a new account. Your application can choose to always send the user to the create an account page by specifying `dwolla_landing=register` in the initial OAuth authorization request.

![Screenshot of Direct OAuth account creation](/images/oauth/1-CreateDirectAcct.png "Dwolla Direct account creation")

#### Step 3: User accepts permissions

User is asked to grant requested permissions to the application. 

![Screenshot of permissons page](/images/oauth/2-GrantPermissions.png "OAuth permissions page")

#### Step 4: Adding a bank account
 There are two experiences available for adding a funding source (aka Bank Account) within the OAuth flow. If the user requires the ability to send money, be sure to request the `Send` and `Funding` scopes, which will trigger the Instant Account Verification (IAV) process, allowing the user to verify and connect a funding source by entering their online banking credentials. 

Alternatively, if the user only needs to receive money, do not request the `Send` scope, but do request the `Funding` scope. This will prompt the user to enter in their bank or credit union account number and routing number, but the IAV process will not be included.

If `Funding` scope is requested but `Send` scope is not, send user through the flow to add a unverified bank account.

![Screenshot of OAuth add unverified bank page](/images/oauth/3-AddNonVerifiedFS.png "OAuth unverified funding source")

If `Send` and `Funding` scopes are requested, send the user through the IAV flow to add a verified bank.

![Screenshot of OAuth add verified bank page](/images/oauth/4-BankSearch.png "OAuth verified funding source")

**Note:** An extensive list of popular U.S. financial institutions are supported by IAV. In the case that a user's bank account is not supported, they'll be unable to complete account creation.

Note: there may be multifactor authentication (MFA) in this step, depending on the financial institution. Different kinds of MFA include:

* Question and answer
* Token (SMS or email verification code)
* CAPTCHA or security image

#### Step 5: Submit and verify bank information 
Once the user selects submit and verify on their bank information, they are then redirected back to your site where you will receive an access token and refresh token pair.

## User experience - Full Dwolla account
To trigger full Dwolla account creation within the OAuth flow instead, set the parameter `verified_account` to true in the initial request for authorization. If you request the `Send` scope then the user will be prompted to add a verified bank after granting permissions to the application.

##### Example URL format:

`https://www.dwolla.com/oauth/v2/authenticate?client_id={client_id}&response_type=code&redirect_uri={redirect_uri}&scope={scope}&verified_account=true&dwolla_landing=register`

#### Step 1: Redirect the user to Dwolla
From your application, the user is presented with Dwolla OAuth in a pop-up or their window is redirected.

#### Step 2: Create or login with existing Dwolla account
User is prompted to create a Dwolla account (or log in with an existing Dwolla account). If Dwolla detects no prior user sessions from dwolla.com, the main call to action will be to create a new account. Your application can choose to always send the user to the create an account page by specifying `dwolla_landing=register` in the initial OAuth authorization request.

![Screenshot of OAuth full account creation](/images/oauth/8-CreateAcct.png "OAuth full account creation")

#### Step 3: Select Dwolla account type
The user will then be prompted to select the type of account to create (Personal, Business, Government, Non-Profit).

![Screenshot of OAuth full account selection page](/images/oauth/9-SelectAcctType.png "OAuth full account selection")

#### Step 4: Complete account registration
The user will then be required to enter information that will be used to verify their identity.

![Screenshot of OAuth business creation page](/images/oauth/10-BizReg.png "OAuth full business account creation")

#### Step 4a: Optional - Upload a document to complete account verification 
Business users may be asked for additional information to complete verification, depending on our ability to verify the information provided until this point.

![Screenshot of OAuth document upload page](/images/oauth/12-Document.png "OAuth document upload")

#### Step 5: User accepts permissions
After completing account registration, the user will be asked to grant permissions to the application. If the `Send` scope is requested, they will be prompted to add a verified bank account via IAV. (This step can be skipped, and the user can choose to add a funding source later on dwolla.com.)

![Screenshot of OAuth permissions page](/images/oauth/2-GrantPermissions.png "OAuth permissions page")

#### Step 6: Optional - Add a bank account
Add a bank or skip to complete the OAuth process

![Screenshot of OAuth add verified bank page](/images/oauth/15-VerifiedBankSearch.png "OAuth add verified bank")

<nav class="pager-nav">
    <a href="authorization-code-flow.html">Back: Authorization code flow</a>
    <a href="using-an-access-token.html">View: Using an access token</a>
</nav>