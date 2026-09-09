# Social Sharing

Manage social media sharing settings, including default messages (quotes), titles, and custom sharing URLs/hashtags for various platforms.

## Endpoints

| Method   | Endpoint & Description                                                                                                                       |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`    | <p><code>/organization/{orgId}/campaign/{id}/share</code><br><strong>List Settings.</strong> Get all social sharing configurations.</p>      |
| `POST`   | <p><code>/organization/{orgId}/campaign/{id}/share</code><br><strong>Update/Create.</strong> Add or update a sharing setting.</p>            |
| `DELETE` | <p><code>/organization/{orgId}/campaign/{id}/share/{social_network}</code><br><strong>Delete.</strong> Remove a specific social setting.</p> |

***

## 1. List Social Settings

Retrieve all active social sharing configurations for the campaign.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/share`

**Response:**

```json
{
  "data": [
    {
      "type": "campaign_share",
      "id": "6091",
      "attributes": {
        "active": true,
        "campaign_id": 45788,
        "social": "facebook",
        "title": "Support our Cause!",
        "quote": "Join me in supporting this amazing campaign!",
        "url_for_share": "https://charidy.com/cmp/share"
      }
    },
    {
      "type": "campaign_share",
      "id": "6092",
      "attributes": {
        "social": "whatsapp",
        "quote": "Check this out: https://charidy.com/cmp"
      }
    }
  ]
}
```

***

## 2. Update Sharing Settings

Create or update the configuration for a specific social network. The system maps the configuration based on the `social` attribute.

**Endpoint:** `POST /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/share`

**Request Body:**

```json
{
  "data": {
    "type": "campaign_share",
    "attributes": {
      "campaign_id": 45788,
      "social": "whatsapp",
      "title": "Message Title",
      "quote": "I just donated! Join me here: ",
      "url_for_share": "https://dashboard.charidy.com/campaign/45788#share",
      "hashtag": "CharidyCampaign",
      "active": true
    }
  }
}
```

### Attributes

| Attribute       | Type    | Description                                                                    |
| --------------- | ------- | ------------------------------------------------------------------------------ |
| `social`        | String  | **Required.** Network code: `facebook`, `whatsapp`, `twitter`, `email`, `sms`. |
| `quote`         | String  | The default message/body text pre-filled for user sharing.                     |
| `title`         | String  | Title of the share (used primarily for Facebook and Email subjects).           |
| `url_for_share` | String  | Custom URL to share. If empty, defaults to the campaign page URL.              |
| `hashtag`       | String  | Hashtag to include (e.g., for Twitter).                                        |
| `active`        | Boolean | Whether this specific sharing option is enabled on the campaign page.          |

***

## 3. Delete Sharing Reference

Removes the custom configuration for a specific network, effectively reverting it to system defaults.

**Endpoint:** `DELETE /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/share/{social_network}`

**Parameters:**

* `{social_network}`: The code of the network to reset (e.g., `facebook`, `whatsapp`).

**Example:** `DELETE /orgarea/api/v1/organization/10001/campaign/45788/share/facebook`
