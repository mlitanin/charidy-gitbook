# Retrieve Campaign

The Campaign API primarily uses a **JSON:API** style response structure. When you request a campaign, you get the core attributes by default, but you must explicitly ask for related resources (like statistics, media, or stories) using the `extend` parameter.

## Endpoints

| Method | Endpoint & Description                                                                                 |
| ------ | ------------------------------------------------------------------------------------------------------ |
| `GET`  | <p><code>/campaign/{id}</code><br><strong>Fetch One.</strong> Get a single campaign by ID.</p>         |
| `GET`  | <p><code>/campaigns</code><br><strong>List.</strong> Get a list of campaigns (supports filtering).</p> |

## 1. Get Campaign

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}`

### Query Parameters

| Parameter | Type    | Description                                                                                                                           |
| --------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `extend`  | `array` | A list of related resources to include in the response. You can pass multiple values (e.g., `?extend=content&extend=campaign_stats`). |

### Available `extend` Resources

| Resource           | Description                                                        |
| ------------------ | ------------------------------------------------------------------ |
| `campaign_stats`   | Live statistics (total raised, donor count).                       |
| `content`          | The campaign's "About" content blocks (About Story).               |
| `media`            | Images and videos associated with the campaign.                    |
| `meta`             | Campaign settings and configurations (pixels, analytics, toggles). |
| `matchers`         | List of matching donors/groups.                                    |
| `donation_levels`  | The preset donation buttons/amounts.                               |
| `donation_streams` | Live feed of recent donations.                                     |
| `organization`     | Details about the parent organization.                             |
| `givingday_stats`  | Specific stats for giving days.                                    |
| `url_alias`        | Custom short links for the campaign.                               |

### Example Request

```http
GET https://dashboardapi.charidy.com/orgarea/api/v1/organization/10001/campaign/45788?extend=campaign_stats&extend=content&extend=media
```

### Example Response structure

The related objects are returned in the **`included`** array, while the main campaign object is in **`data`**.

```json
{
  "data": {
    "type": "campaign",
    "id": "45788",
    "attributes": {
      "title": "Summer Camp Campaign",
      "goal": 1000000,
      "currency": "ils",
      "start_date": 1767398400,
      "end_date": 1768694400
    },
    "relationships": {
      "campaign_stats": { "data": { "total": 50000, "donors_total": 120 } },
      "campaign_content": { 
        "data": [
          { "type": "content", "id": "54554" } 
        ]
      }
    }
  },
  "included": [
    {
      "type": "content",
      "id": "54554",
      "attributes": { "language": "en", "title": "Our Story...", "content": "..." }
    },
    {
      "type": "media",
      "id": "123",
      "attributes": { "type": "image", "url": "https://..." }
    }
  ]
}
```

> **Tip:** Efficient Loading Always request only the `extend` resources you actually need to keep the response size small and fast. For example, if you just need the top banner, only request `extend=media`.

***

## 2. List Campaigns

Retrieve a paginated list of campaigns for a specific organization.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/campaigns`

### Query Parameters

| Parameter           | Type      | Description                                                                                                               |
| ------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------- |
| `page`              | `integer` | Page number (default: 1).                                                                                                 |
| `limit` / `perPage` | `integer` | Number of items per page (default: 10, max: 50).                                                                          |
| `sort_by[]`         | `array`   | Sort fields. Prefix with `-` for descending order (e.g., `-startdate`, `campaign_mode_dashboard`, `hide_directdonation`). |
| `extend[]`          | `array`   | Include related resources (see table below).                                                                              |

### Available `extend` Options

The same `extend` options available for the single campaign endpoint are also supported here, allowing you to fetch rich data for multiple campaigns in a single request.

| Value              | Description                                                              |
| ------------------ | ------------------------------------------------------------------------ |
| `campaign_stats`   | Includes `campaign_stats` object with `total` raised and `donors_total`. |
| `media`            | Includes campaign images and videos (hero images, logos, etc).           |
| `meta`             | Includes campaign configuration metadata (analytics, toggles).           |
| `donation_levels`  | Includes the preset donation amounts.                                    |
| `donation_streams` | Includes recent donation stream items.                                   |
| `matchers`         | Includes information about matchers.                                     |

### Pagination Headers

The API returns pagination metadata in the response headers:

| Header            | Description                                       |
| ----------------- | ------------------------------------------------- |
| `x-total-records` | The total number of campaigns matching the query. |
| `x-search-page`   | The current page number.                          |
| `x-search-limit`  | The limit (per page) used.                        |

### Example Request

```http
GET https://dashboardapi.charidy.com/orgarea/api/v1/organization/4986/campaigns?page=1&perPage=10&sort_by[]=-startdate&extend[]=campaign_stats&extend[]=media
```

### Example Response

```json
{
  "data": [
    {
      "type": "campaign",
      "id": "45788",
      "attributes": {
        "title": "Winter Drive",
        "goal": 500000,
        "currency": "usd",
        "start_date": 1767398400,
        "end_date": 1768694400,
        "campaign_stats": {
          "total": 125000,
          "donors_total": 450
        }
      },
      "relationships": {
        "campaign_media": {
          "data": [
            { "type": "media", "id": "90608" }
          ]
        }
      }
    },
    {
      "type": "campaign",
      "id": "45789",
      "attributes": {
        "title": "Purim Campaign",
        "goal": 18000,
        "currency": "usd",
        "start_date": 1770000000,
        "end_date": 1770500000,
        "campaign_stats": null
      }
    }
  ],
  "included": [
    {
      "type": "media",
      "id": "90608",
      "attributes": {
        "src": "https://cdn.charidy.com/...",
        "tag": "campaign_hero_mobile"
      }
    }
  ]
}
```
