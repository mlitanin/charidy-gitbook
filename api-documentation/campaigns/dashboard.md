# Dashboard & Stats

The Campaign Dashboard provides real-time analytics, activity feeds, and performance metrics for your campaign.

## Endpoints

| Method | Endpoint & Description                                                                                                                                              |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`  | <p><code>/organization/{orgId}/campaign/{id}/dashboard</code><br><strong>Get Dashboard Data.</strong> Retrieve comprehensive campaign statistics and activity.</p>  |
| `GET`  | <p><code>/organization/{orgId}/campaigns?extend[]=campaign_stats</code><br><strong>List Campaigns with Stats.</strong> Get all campaigns with their statistics.</p> |

## 1. Get Campaign Dashboard

Retrieve comprehensive dashboard data including activity feed, statistics, and charts.

**Endpoint:** `GET /organization/{orgId}/campaign/{id}/dashboard`

**Query Parameters:**

| Parameter | Type   | Description                                                                                                                                                                                                                                              |
| --------- | ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `extend`  | String | <p>Include additional data. Multiple values supported:<br>• <code>campaign_stats</code> - Campaign statistics<br>• <code>content</code> - Campaign content<br>• <code>media</code> - Campaign media<br>• <code>matchers</code> - Matcher information</p> |

**Example Request:**

```http
GET /organization/10001/campaign/45788/dashboard?extend=campaign_stats&extend=content&extend=media&extend=matchers
```

**Response:**

```json
{
  "data": {
    "type": "",
    "attributes": {
      "activity_list": [
        {
          "id": 0,
          "type": "new donation",
          "date": "2026-01-03T19:40:28Z",
          "details": {
            "id": 18389886,
            "campaign_id": 45788,
            "customer_id": 0,
            "processing_charged_amount": 34,
            "fee_cover_amount": 0,
            "processing_charged_currency": "ils",
            "charge_amount": 34,
            "effective_amount": 68,
            "donor_name": "John Doe",
            "email": "donor@example.com",
            "phone": "+1 555 123 4567",
            "status": "Processed",
            "dedication": "In honor of...",
            "team_names": ["Team Alpha"]
          }
        }
      ],
      "chart": {
        "labels": ["Jan 1", "Jan 2", "Jan 3"],
        "datasets": [
          {
            "label": "Donations",
            "data": [100, 250, 180]
          }
        ]
      },
      "currency_code": "ils",
      "currency_sign": "₪",
      "total_donations": 15,
      "total_raised": 5000,
      "goal": 10000,
      "percentage": 50,
      "donors_count": 12,
      "average_donation": 333
    }
  }
}
```

### Dashboard Attributes

| Attribute          | Type    | Description                                                |
| ------------------ | ------- | ---------------------------------------------------------- |
| `activity_list`    | Array   | Recent campaign activity (donations, team creation, etc.). |
| `chart`            | Object  | Chart data for visualizing donation trends over time.      |
| `currency_code`    | String  | Campaign currency code (e.g., `usd`, `ils`).               |
| `currency_sign`    | String  | Currency symbol (e.g., `$`, `₪`).                          |
| `total_donations`  | Integer | Total number of donations received.                        |
| `total_raised`     | Number  | Total amount raised (in cents/agorot).                     |
| `goal`             | Number  | Campaign goal amount (in cents/agorot).                    |
| `percentage`       | Number  | Percentage of goal reached.                                |
| `donors_count`     | Integer | Number of unique donors.                                   |
| `average_donation` | Number  | Average donation amount (in cents/agorot).                 |

### Activity List

Each activity item in the `activity_list` array contains:

| Field     | Type    | Description                                                             |
| --------- | ------- | ----------------------------------------------------------------------- |
| `id`      | Integer | Activity ID.                                                            |
| `type`    | String  | Activity type (`"new donation"`, `"new team"`, `"goal reached"`, etc.). |
| `date`    | String  | ISO 8601 timestamp of the activity.                                     |
| `details` | Object  | Activity-specific details (varies by type).                             |

**Activity Types:**

* `"new donation"` - A new donation was received
* `"new team"` - A new team was created
* `"goal reached"` - Campaign goal was reached
* `"matcher activated"` - A matcher pledge was activated

### Chart Data

The `chart` object provides data for visualizing donation trends:

```json
{
  "labels": ["Day 1", "Day 2", "Day 3"],
  "datasets": [
    {
      "label": "Donations",
      "data": [1000, 2500, 1800]
    },
    {
      "label": "Donors",
      "data": [10, 25, 18]
    }
  ]
}
```

***

## 2. List Campaigns with Statistics

Retrieve all campaigns for an organization with their statistics included.

**Endpoint:** `GET /organization/{orgId}/campaigns?extend[]=campaign_stats`

**Query Parameters:**

| Parameter   | Type    | Description                                                                                                                                                                                                                                                             |
| ----------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `extend[]`  | Array   | <p>Include additional data:<br>• <code>campaign_stats</code> - Statistics<br>• <code>media</code> - Campaign media<br>• <code>meta</code> - Meta settings<br>• <code>donation_levels</code> - Donation levels<br>• <code>donation_streams</code> - Donation streams</p> |
| `sort_by[]` | Array   | Sort criteria (e.g., `-startdate`, `campaign_mode_dashboard`).                                                                                                                                                                                                          |
| `page`      | Integer | Page number for pagination (default: 1).                                                                                                                                                                                                                                |
| `per_page`  | Integer | Results per page (default: 10, max: 100).                                                                                                                                                                                                                               |

**Example Request:**

```http
GET /organization/10001/campaigns?extend[]=campaign_stats&extend[]=media&extend[]=meta&sort_by[]=-startdate&page=1&per_page=10
```

**Response:**

```json
{
  "data": [
    {
      "type": "campaign",
      "id": "45788",
      "attributes": {
        "title": "Annual Fundraiser 2026",
        "campaign_stats": {
          "total": 5000,
          "donors_total": 125,
          "goal": 10000,
          "percentage": 50
        },
        "currency": "usd",
        "currency_sign": "$",
        "start_date": 1767398400,
        "end_date": 1768694400,
        "mode": 2,
        "template": "unidy-v2"
      }
    }
  ]
}
```

### Campaign Stats Object

When `extend[]=campaign_stats` is included, each campaign includes a `campaign_stats` object:

| Field              | Type    | Description                            |
| ------------------ | ------- | -------------------------------------- |
| `total`            | Number  | Total amount raised (in cents/agorot). |
| `donors_total`     | Integer | Total number of unique donors.         |
| `goal`             | Number  | Campaign goal (in cents/agorot).       |
| `percentage`       | Number  | Percentage of goal reached.            |
| `donations_count`  | Integer | Total number of donations.             |
| `teams_count`      | Integer | Number of teams created.               |
| `average_donation` | Number  | Average donation amount.               |

***

## Real-Time Updates

The dashboard data is updated in real-time as donations come in. For live updates:

1. **Polling:** Call the dashboard endpoint every 10-30 seconds
2. **WebSockets:** (If available) Subscribe to campaign events for instant updates

> **Tip:** Cache dashboard data for 10-30 seconds to reduce API calls while still providing near-real-time updates.

***

## Use Cases

### Display Campaign Progress

```javascript
const response = await fetch(
  '/organization/10001/campaign/45788/dashboard?extend=campaign_stats',
  {
    headers: { 'Authorization': `Bearer ${token}` }
  }
);

const data = await response.json();
const { total_raised, goal, percentage } = data.data.attributes;

console.log(`Raised: ${total_raised / 100} / ${goal / 100} (${percentage}%)`);
```

### Show Recent Activity

```javascript
const { activity_list } = data.data.attributes;

activity_list.forEach(activity => {
  if (activity.type === 'new donation') {
    console.log(`New donation from ${activity.details.donor_name}: $${activity.details.effective_amount / 100}`);
  }
});
```

### Visualize Donation Trends

```javascript
const { chart } = data.data.attributes;

// Use with Chart.js, D3.js, or any charting library
new Chart(ctx, {
  type: 'line',
  data: {
    labels: chart.labels,
    datasets: chart.datasets
  }
});
```

***

## Performance Metrics

The dashboard provides key performance indicators (KPIs) for campaign analysis:

* **Conversion Rate:** Percentage of visitors who donated
* **Average Donation:** Total raised / Number of donations
* **Donor Retention:** Percentage of repeat donors
* **Team Performance:** Top-performing teams by amount raised
* **Time-based Trends:** Donation patterns by hour/day/week

> **Warning:** Dashboard data is cached for performance. There may be a delay of up to 60 seconds for very recent donations to appear.
