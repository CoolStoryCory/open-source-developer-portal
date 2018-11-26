---
layout: twoColumn
section: resources
type: article
title:  Bank transfer workflow
description: Most bank transfers initiated within the Dwolla network are processed via the Automated Clearing House (ACH).
---

# Bank transfer workflow

Most bank transfers initiated within the Dwolla network are processed via the Automated Clearing House (ACH). We’ve reduced the complex nature of the ACH protocol down to several distinct statuses for transfers. Once a transfer is created it is recorded as an event (`bank_transfer_created` and `transfer_created`) and will be sent to you via a webhook (if subscribed). You will receive subsequent webhooks as the transfer follows the workflow outlined in this article. Webhooks will notify your application of a transfer status changes.

## Transfer statuses

A normal ACH transfer workflow includes the following statuses:

- **Pending:** A transfer is initiated and starts off as “pending.” A transfer is pending when the money has yet to leave the funding source or it is en route to its destination.
- **Processed:** Once the ACH transfer has cleared successfully, it will be marked as “processed.” A processed transfer represents money that has reached its destination account—either a balance or bank account. For a balance, funds will become available to the recipient, while for a bank account, it may take additional time to become available, depending on your bank. Note: “processed” is not necessarily a final state. (See “failed” status below for additional context.)

Between the ACH transfer statuses of “pending” and “processed” there are two other transfer statuses which can occur: “cancelled” or “failed”.

- **Cancelled:** If a transfer’s status changes from “pending” to “cancelled”, this means that the transfer was cancelled. See "Bank to Dwolla network" and "Dwolla network to bank" in the [processing times](/resources/bank-transfer-workflow/processing-times.html) section for available cancellation windows.
- **Failed:** If a transfer failed to clear successfully (usually as a result of an ACH reject or return), the transfer’s status will be “failed”. Transfers can fail for a number of reasons, e.g. insufficient funds, invalid account number, no account/unable to locate account, etc. Note: in rare cases, a “processed” transfer may later on get returned as “failed”.

* * *

#### Learn more about bank transfers

* [Processing times](/resources/bank-transfer-workflow/processing-times.html)
* [Transfer failures](/resources/bank-transfer-workflow/transfer-failures.html)
