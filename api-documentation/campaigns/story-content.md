# Story & Content

The **Campaign Story** (often referred to as "About" or "Description") is the heart of your fundraising page. The API allows you to manage this content dynamically, supporting **multiple languages** and **conditional displays** (tags) for different campaign phases.

## Overview

Content is not stored directly on the Campaign object. Instead, it is stored as separate **Content Resources** linked to the campaign.

* **Multi-Language:** You can create separate content blocks for `en`, `he`, `fr`, etc.
* **HTML Support:** The `content` field accepts full HTML (Rich Text).
* **Smart Tags:** Define when content appears (e.g., only during "Bonus Round" or "Countdown").

***

## Endpoints

| Method   | Endpoint & Description                                                                                                                                 |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `GET`    | <p><code>/campaign/{id}?extend=content</code><br><strong>Retrieve Content.</strong> Returns all content blocks in the <code>included</code> array.</p> |
| `POST`   | <p><code>/campaign/{id}/content</code><br><strong>Create Content.</strong> Add a new translation or specific phase content.</p>                        |
| `PUT`    | <p><code>/campaign/{id}/content/{contentId}</code><br><strong>Update Content.</strong> Edit an existing content block.</p>                             |
| `DELETE` | <p><code>/campaign/{id}/content/{contentId}</code><br><strong>Delete Content.</strong> Remove a content block.</p>                                     |

***

## 1. Retrieve Content

To see the existing "About" text, you must fetch the campaign with `extend=content`.

**Endpoint:** `GET .../campaign/{id}?extend=content`

The content objects will appear in the `included` array of the response.

### Response Example

```json
{
  "data": {
    "type": "campaign",
    "id": "45788",
    "relationships": {
      "campaign_content": {
        "data": [
          { "type": "content", "id": "54554" },
          { "type": "content", "id": "54561" }
        ]
      }
    }
  },
  "included": [
    {
      "type": "content",
      "id": "54554",
      "attributes": {
        "language": "en",
        "tag": "", 
        "title": "Our Mission",
        "content": "<p>Join us in making a difference...</p>"
      }
    },
    {
      "type": "content",
      "id": "54561",
      "attributes": {
        "language": "he",
        "tag": "bonus_goal",
        "content": "<p>טקסט שמופיע רק בשלב הבונוס...</p>"
      }
    }
  ]
}
```

***

## 2. Create Content

Add a new translation or content block for a specific phase (tag).

**Endpoint:** `POST .../campaign/{campaignId}/content`

### Request Body

```json
{
  "data": {
    "attributes": {
      "campaign_id": 45788,
      "language": "en",
      "tag": "bonus_goal",
      "title": "Bonus Goal!",
      "content": "<p>We hit our goal! Now let's go for the bonus!</p>"
    }
  }
}
```

***

## 3. Update Content

To edit a specific block (e.g., fix a typo in the English description), you update its specific content ID.

**Endpoint:** `PUT .../campaign/{campaignId}/content/{contentId}`

### Request Body

```json
{
  "data": {
    "type": "content",
    "id": "54554",
    "attributes": {
      "campaign_id": 45788,
      "language": "en",
      "tag": "",
      "title": "New Headline",
      "content": "<p><strong>Updated</strong> story content goes here.</p>"
    }
  }
}
```

### Fields Reference

| Field      | Type      | Description                                                                                   |
| ---------- | --------- | --------------------------------------------------------------------------------------------- |
| `language` | `string`  | The 2-letter language code (e.g., `en`, `he`, `fr`).                                          |
| `content`  | `html`    | The rich text description of the campaign.                                                    |
| `tag`      | `string`  | **Crucial for logic.** controls _when_ this text appears.                                     |
| `team_id`  | `integer` | Optional. Associates content with a specific team (default `0` for general campaign content). |

### Available Tags

Use these tags to control the **context** in which the specific content block appears.

| Tag Value           | Context / Meaning                                                                                               |
| ------------------- | --------------------------------------------------------------------------------------------------------------- |
| `""` (Empty String) | **Default / Standard**. This is the main campaign description shown by default.                                 |
| `primary_goal`      | **Primary Goal Phase**. Displayed specifically while the campaign is in its first goal phase.                   |
| `bonus_goal`        | **Bonus Round**. Displayed when the campaign enters the bonus round.                                            |
| `countdown`         | **Countdown / Teaser**. Displayed on the landing page before the campaign starts (if countdown mode is active). |
| `completed`         | **Campaign Ended**. Displayed after the campaign has finished.                                                  |

***

## 4. Delete Content

Remove a specific translation or content block.

**Endpoint:** `DELETE .../campaign/{campaignId}/content/{contentId}`

### Example Request

```http
DELETE https://dashboardapi.charidy.com/orgarea/api/v1/organization/10001/campaign/45788/content/54558
```

### Example Response

```json
{
  "Result": "ok"
}
```
