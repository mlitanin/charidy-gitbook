# Create & Update

This section details how to create new **Campaign Objects** and update existing ones.

## Endpoints

| Method | Endpoint & Description                                                                                                                  |
| ------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| `POST` | <p><code>/organization/{orgId}/campaigns</code><br><strong>Create Campaign.</strong> Launch a new fundraising campaign.</p>             |
| `PUT`  | <p><code>/organization/{orgId}/campaign/{campaignId}</code><br><strong>Update Campaign.</strong> Modify settings, goals, and dates.</p> |

***

## 1. Create Campaign

To launch a new fundraising page, send a `POST` request to the campaigns collection.

**Endpoint:** `POST https://dashboardapi.charidy.com/orgarea/api/v1/organization/{orgId}/campaigns`

### Request Body

```json
{
  "data": {
    "attributes": {
      "title": "Annual Charity Gala 2025",
      "currency": "ils",
      "goal": 500000,
      "start_date": 1767398400,
      "end_date": 1768694400
    }
  }
}
```

| Field      | Type      | Required | Description                    |
| ---------- | --------- | :------: | ------------------------------ |
| `title`    | `string`  |     ✅    | The name of the campaign.      |
| `currency` | `string`  |     ✅    | ISO code (e.g., `ils`, `usd`). |
| `goal`     | `integer` |     ✅    | Fundraising target amount.     |

***

## 2. Update Campaign

Modify an existing campaign using `PUT`.

**Endpoint:** `PUT https://dashboardapi.charidy.com/orgarea/api/v1/organization/{orgId}/campaign/{campaignId}`

### Request Body

```json
{
  "data": {
    "attributes": {
      "title": "Updated Title",
      "primary_goal": 200000,
      "bonus_goal": 250000,
      "display_end_date": 1768694400
    }
  }
}
```

### Campaign Attributes Reference

The following attributes are available when creating or updating a campaign.

| Attribute                 | Type        | Description                                                                       |
| ------------------------- | ----------- | --------------------------------------------------------------------------------- |
| `title`                   | `string`    | **Required.** The public display name of the campaign.                            |
| `currency`                | `string`    | **Required.** ISO 4217 Currency Code (e.g., `ils`, `usd`, `eur`).                 |
| `goal`                    | `integer`   | **Required (Create).** The initial fundraising target.                            |
| `primary_goal`            | `integer`   | The main goal displayed on the progress bar.                                      |
| `primary_goal_multiplier` | `integer`   | Matcher ratio for the primary round (e.g., `2` for 1:1 match, `4` for 1:3 match). |
| `bonus_goal`              | `integer`   | The target for the "Bonus Round" (must be > `primary_goal`).                      |
| `bonus_goal_multiplier`   | `integer`   | Matcher ratio for the bonus round.                                                |
| `start_date`              | `timestamp` | Unix timestamp (seconds) for when the campaign goes live.                         |
| `end_date`                | `timestamp` | Unix timestamp (seconds) for the campaign deadline.                               |
| `display_end_date`        | `timestamp` | Unix timestamp. Controls the visible countdown timer. Usually same as `end_date`. |
| `short_link`              | `string`    | Custom URL slug (e.g., `charidy.com/cmp/SLUG`).                                   |
| `description`             | `html`      | HTML content for the main campaign story (legacy field; prefer **Content API**).  |
| `show_on_org_page`        | `boolean`   | If `true`, this campaign appears on the Organization's public profile.            |
| `facebook_pixel_id`       | `string`    | Facebook Pixel ID for tracking.                                                   |
| `google_analytics_number` | `string`    | GA4 Measurement ID (e.g., `G-XXXXXX`).                                            |
| `google_conversion_id`    | `string`    | Google Ads Conversion ID.                                                         |
| `google_conversion_label` | `string`    | Google Ads Conversion Label.                                                      |
| `rounds`                  | `array`     | Read-only. Calculated structure of goals and multipliers.                         |
| `mode`                    | `integer`   | Internal mode flag (e.g., `5`).                                                   |
| `category`                | `string`    | Campaign category (e.g., `regular`, `crowdfunding`).                              |

> **Tip:** Pro Tip Use the `Extend` parameter in GET requests to retrieve these updated fields immediately after saving.
