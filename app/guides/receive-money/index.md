---
layout: twoColumn
section: guides
type: guide
guide: 
    name: receive-money
    step: overview
title:  Receive bank transfers within your application
description: Programmatically collect money from customer bank accounts with bank transfers. 
---

# Overview: Receive money from your users

In this guide, we’ll cover the key points of collecting money from your customers by utilizing our bank transfer API.

- Create a sending customer for the funds
- Associate a verified funding source (bank or credit union account) with the customer
- Transfer funds from the customer’s linked funding source (Bank Account) 

*Prerequisite*: complete the [Getting started guide](/guides/sandbox-setup).

### Choose the experience your want for your customers

Dwolla offers two different paths for you to onboard your customers onto the payment platform. If you want to keep Dwolla in the background, choose our [Access API](https://www.dwolla.com/products/access-api). Otherwise, tap into Dwolla’s co-branded OAuth experience with [Transfer](https://www.dwolla.com/products/transfer). [Contact sales](https://www.dwolla.com/contact) for information about pricing for each option. 

Regardless of which option you implement, the first step is to create senders for your transfer, along with a funding source (Bank Account) where the money will be sent from. Dwolla’s Transfer experience prompts your senders for their bank or credit union account information. Otherwise, if you choose to integrate with the Access API, your application will need to capture these fields to pass through to Dwolla for secure storage.

<nav class="decision-nav">
    <div>
        <a href="access-api-onboarding.html">
            <div class="icon-decision-nav-white-label"></div>
            Access API
        </a>
        <p>Customers use your interface</p>
    </div>
    <div>
        <a href="transfer-onboarding.html">
            <div class="icon-decision-nav-direct"></div>
            Transfer
        </a>
        <p>Customers use the Dwolla interface</p>
    </div>
</nav>
