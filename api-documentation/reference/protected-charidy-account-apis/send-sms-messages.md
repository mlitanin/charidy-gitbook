---
description: >-
  https://dashboardapi.charidy.com/orgarea/api/v1/organization/{org+id}/campaign/{campaign_id}/messaging/sms/donors
---

# Send SMS messages

## Data flow

Once the APi called with a payload example:

```
{
    "campaign_id":21075,
    "message":"test{{.Name}}",
    "donor_ids":[7130786]
}
```

We create tasks:

1. Task: orgmessage
2. Worker: generate

Using the SQS:&#x20;

1. production\_orgmessage\_generate.fifo
2. production\_orgmessage\_sms.fifo

