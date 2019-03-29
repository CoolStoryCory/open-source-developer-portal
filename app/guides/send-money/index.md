---
layout: twoColumn
section: guides
type: guide
guide:
    name: send-money
    step: overview
title:  ACH Transfers Overview
description: This guide covers the three key points of sending money to your users through your application and the Dwolla API.
---
# Send money to your users

## Overview

This guide is designed to get you up and running quickly through creating a one-time transfer to an end user via the Dwolla API. In this guide we’ll cover the basics of integrating the most lightweight payment flow, sending funds (also referred to as “payouts”), by outlining and walking through the necessary steps, to create a bank transfer. For simplicity, we’ll represent a one-to-one transfer between two users, where the source user is identified as the Master Dwolla account and the destination user is an individual or business that has been on-boarded via the Dwolla API. 

If your use case involves sending many payments in a single batch, it’s recommended that you leverage our [Mass Payment API](https://docs.dwolla.com/#create-a-customer) which allows you to send up to 5000 payments with a single API request.

<img src="/images/funds_flow_send.gif" width="75%" height="75%">

### Key Concepts

In this quickstart guide, you’ll learn the three key concepts involved with sending money to a recipient’s bank account:

1. Choosing and creating the Customer record type for your recipient
2. Attaching a funding source (bank account) to the receive-only User
3. Fetching the available funding sources
4. Sending funds to the receive-only User

## Before you begin

We encourage you to create a sandbox account, if you haven’t already. This will allow you to follow along with the steps outlined in this guide. [Check out our Sandbox setup guide to learn more.](https://developers.dwolla.com/guides/sandbox-setup/)

After creating a sandbox account, you’ll obtain your API Key and Secret, which are used to obtain an OAuth access token. An access token is required in order to authenticate against the Dwolla API. Learn more about how to [obtain an access token in our guide.](https://developers.dwolla.com/guides/auth/)

Lastly, in this sandbox walkthrough, we recommend having an active webhook subscription. This will help notify your application of various events that occur within Dwolla. [Check out our guide to learn more.](https://developers.dwolla.com/guides/webhooks/)

**Let’s get started!**

<nav class="pager-nav">
    <a href="" style="display:none;"></a>
    <a href="onboarding.html">Next: Customer onboarding</a>
</nav>
