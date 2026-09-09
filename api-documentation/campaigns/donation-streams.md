# Donation Streams

Donation Streams allow you to create multiple "funding buckets" or specific goals within a single campaign. Each stream has its own goal, progress tracking, and visual identity (color/image).

## Overview

Streams are useful for:

* Allow donors to choose where their money goes (e.g., "Build a library" vs. "Buy books").
* Breaking down a large campaign goal into tangible milestones.
* Visualizing different projects supported by the organization.

***

## Endpoints

| Method   | Endpoint & Description                                                                                                                            |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`    | <p><code>/organization/{orgId}/campaign/{id}/donation_streams</code><br><strong>List Streams.</strong> Get all streams for a campaign.</p>        |
| `POST`   | <p><code>/organization/{orgId}/campaign/{id}/donation_streams</code><br><strong>Create Stream.</strong> Add a new donation stream.</p>            |
| `PUT`    | <p><code>/organization/{orgId}/campaign/{id}/donation_streams/{streamId}</code><br><strong>Update Stream.</strong> Modify an existing stream.</p> |
| `DELETE` | <p><code>/organization/{orgId}/campaign/{id}/donation_streams/{streamId}</code><br><strong>Delete Stream.</strong> Remove a stream.</p>           |

***

## 1. List Donation Streams

Retrieve all donation streams configured for the campaign.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/donation_streams`

**Response:**

```json
{
  "data": [
    {
      "type": "donation_stream",
      "id": "3252",
      "attributes": {
        "campaign_id": 45788,
        "title": "Build a Classroom",
        "description": "Help us build a new classroom for the 3rd grade.",
        "color": "#c74d4d",
        "goal": 50000,
        "image": "https://cdn.charidy.com/images/123/classroom.png",
        "order": 1,
        "default": false,
        "visible": true,
        "meta": "{\"show_on_campaign_page\":true,\"show_on_donation_form\":true}"
      }
    }
  ]
}
```

***

## 2. Create Donation Stream

Add a new stream to the campaign.

**Endpoint:** `POST /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/donation_streams`

**Request Body:**

```json
{
  "data": {
    "attributes": {
      "campaign_id": 45788,
      "title": "Scholarship Fund",
      "description": "Support students in need",
      "color": "#4d94c7",
      "goal": 25000,
      "image": "https://cdn.charidy.com/images/123/scholarship.png",
      "order": 2,
      "default": false,
      "meta": "{\"show_on_campaign_page\":true,\"show_on_donation_form\":true}"
    }
  }
}
```

### Attributes

| Attribute     | Type          | Description                                               |
| ------------- | ------------- | --------------------------------------------------------- |
| `title`       | String        | **Required.** Name of the stream.                         |
| `description` | String        | Short description of the cause.                           |
| `goal`        | Integer       | monetary goal for this specific stream.                   |
| `color`       | String        | Hex color code (e.g., `#FF5733`) for the progress bar/UI. |
| `image`       | String        | URL of the stream's image (upload via `/account/media`).  |
| `order`       | Integer       | Display order in the list.                                |
| `default`     | Boolean       | Whether this stream is selected by default.               |
| `meta`        | String (JSON) | JSON string for UI visibility settings (see below).       |

### Meta Configuration

The `meta` field is a stringified JSON object controlling visibility:

```json
{
  "show_on_campaign_page": true,
  "show_on_donation_form": true
}
```

***

## 3. Update Donation Stream

**Endpoint:** `PUT /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/donation_streams/{streamId}`

**Request Body:**

```json
{
  "data": {
    "type": "donation_stream",
    "id": "3252",
    "attributes": {
      "goal": 60000,
      "title": "Build a Large Classroom"
    }
  }
}
```

***

## 4. Delete Donation Stream

**Endpoint:** `DELETE /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/donation_streams/{streamId}`

***

## Image Handling

Images for streams follow the standard [Media Handling](../getting-started/media-handling.md) process:

1. Upload image to `/account/media`.
2. Use the returned URL in the `image` field when creating/updating the stream.
