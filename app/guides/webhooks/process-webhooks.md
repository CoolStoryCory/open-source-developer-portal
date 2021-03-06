---
layout: twoColumn
section: guides
type: guide
guide: 
    name: webhooks
    step: '4'
title: Webhooks | Process Webhooks
description: Process webhooks for payments within your application by utilizing our open API with no per transaction fees. 
---

# Example scenario

Let's assume that you have a webhook subscription and Dwolla has just delivered the following payload to your specified endpoint:

##### Sample Payload
```jsonnoselect
{
  "id": "cac95329-9fa5-42f1-a4fc-c08af7b868fb",
  "resourceId": "cdb5f11f-62df-e611-80ee-0aa34a9b2388",
  "topic": "customer_transfer_created",
  "timestamp": "2017-01-20T22:45:12.790Z",
  "_links": {
    "self": {
      "href": "https://api-sandbox.dwolla.com/events/cac95329-9fa5-42f1-a4fc-c08af7b868fb"
    },
    "account": {
      "href": "https://api-sandbox.dwolla.com/accounts/ad5f2162-404a-4c4c-994e-6ab6c3a13254"
    },
    "resource": {
      "href": "https://api-sandbox.dwolla.com/transfers/cdb5f11f-62df-e611-80ee-0aa34a9b2388"
    },
    "customer": {
      "href": "https://api-sandbox.dwolla.com/customers/e358a488-6699-4d79-bbfb-c5bf58100ea4"
    }
  },
  "created": "2017-01-20T22:45:12.790Z"
}
```

For illustrative purposes, let's assume you are using Ruby on Rails with a controller defined to handle the POST request from Dwolla's servers. Your application logic will look a little something like this:

##### Ruby
```rubynoselect
require 'dwolla_swagger'

if params[:topic] == 'transfer_completed'
  transfer = DwollaSwagger::TransfersApi.by_id(params[:links][:resource][:href])

  transfer._embedded.each do |k, v|
    # Retrieve customer info from your database
    # or from Dwolla here, and then send notifications
  end
elsif params[:topic] == 'another_event'
  "..."
end
```

Let's recap. From the event we can retrieve the `transfer` and then take a look at the `_links` object and retrieve the `customers` that need to be notified. From here, you can make a call to `customers/{id}` to retrieve their e-mail addresses (or to your own database) so that you can send your notification message.

That's it! You’ve learned the basics of webhooks. 

<nav class="pager-nav">
    <a href="./validating-webhooks.html">Back: Validating webhooks</a>
    <a href="" style="display:none;"></a>
</nav>
