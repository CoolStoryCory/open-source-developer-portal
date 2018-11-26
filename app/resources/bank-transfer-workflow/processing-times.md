---
layout: twoColumn
section: Bank transfer workflow
type: article
title:  Processing times
weight: 0
description: ACH Transfers, where the destination is a Dwolla balance, are completed as soon as the funds enter the Dwolla network.
---

# Bank transfer workflow

## Processing times
The scenarios in the table below are composed of two transfers: a bank to Dwolla network transfer and a Dwolla network to bank transfer. Transfers where the destination is a Dwolla balance are complete as soon as the funds enter the Dwolla network, and all transfers into the Dwolla network that are either sourced from Dwolla user balances can be settled in real-time.

![Transfer timing](/images/ACH_Transfer-timeline.png "Dwolla ACH transfer timeline")

#### Customer to customer transfers

| Source of transfer | Time to Dwolla network | Time from Dwolla network to recipient’s bank account | Total time to `processed` |
|:------------- |:--------------|:------|:-----|
| Real-time: Dwolla balance | Instant | 1-2 business days | 1-2 business days |
| Next-day ACH: linked bank account | 1-2 business days | 1-2 business days | 2-4 business days |
| Standard ACH: linked bank account | 3-4 business days | 1-2 business days | 4-6 business days |

This can be compared to typical Standard ACH transfer times of T+3 to T+4—depending on the financial institution, if you have next-day transfers enabled, and if the transfer is initiated before **4 PM CT**.

The timetables for transfers in and out of the Dwolla network are:

## Bank to Dwolla network

Transfers into a Dwolla balance from a bank, including transfers that pass through the balance to another Customer’s bank account, can be cancelled by a [Dwolla Master Account](https://docsv2.dwolla.com/#accounts) or [API Customer account](/resources/account-types.html) at any point until 4pm CT on that same business day if initiated prior to 4PM CT. If a transfer was initiated after 4pm CT, it can be cancelled anytime before 4pm CT on the following business day.

![Transfer timing](/images/ACH_Next-Day.png "Dwolla ACH transfer timing, pay in")

## Dwolla network to bank

Transfers out of the Dwolla network can be cancelled at any point until 4pm CT on that same business day if the transfer was initiated prior to 4PM CT. If a transfer was initiated after 4pm CT, it can be cancelled anytime before 4pm CT on the following business day.

To learn more about how to initiate a Same-Day ACH credit transfer, reference our [developer resource article.](/resources/same-day-ach.html)

![Transfer timing](/images/ACH_Same-Day.png "Dwolla ACH transfer timing, pay out")
