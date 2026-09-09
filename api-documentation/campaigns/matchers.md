# Matchers

Matchers are major donors or sponsors who pledge to multiply donations (e.g., "Every dollar you give is doubled"). They are prominently displayed on the campaign page.

## Endpoints

| Method   | Endpoint & Description                                                                                                                                           |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`    | <p><code>/organization/{orgId}/campaign/{id}?extend=matchers</code><br><strong>Get Matchers.</strong> Retrieved as part of the Campaign Object.</p>              |
| `POST`   | <p><code>/organization/{orgId}/campaign/{id}/matchers</code><br><strong>Create Matcher.</strong> Add a new matcher profile to the campaign.</p>                  |
| `PUT`    | <p><code>/organization/{orgId}/campaign/{id}/matcher/{matcherId}</code><br><strong>Update Matcher.</strong> Modify details like name, image, or description.</p> |
| `DELETE` | <p><code>/organization/{orgId}/campaign/{id}/matcher/{matcherId}</code><br><strong>Delete Matcher.</strong> Remove a matcher.</p>                                |

***

## 1. Get Matchers

Matchers are fetched by including `extend=matchers` in the Campaign request.

**Endpoint:** `GET /api/v1/organization/{orgId}/campaign/{id}?extend=matchers`

**Response Structure:** Matchers appear in the `included` array with `type: "matcher"`.

```json
{
  "data": {
    "type": "campaign",
    "relationships": {
      "campaign_matcher": {
        "data": [
          { "type": "matcher", "id": "56515" }
        ]
      }
    }
  },
  "included": [
    {
      "type": "matcher",
      "id": "56515",
      "attributes": {
        "name": "Goldman Foundation",
        "description": "<p>Matching every dollar up to $100k</p>",
        "image": "https://cdn.charidy.com/images/matcher-logo.png",
        "active": true,
        "featured": true,
        "goal": 100000,
        "order": 1,
        "campaign_id": 45788
      }
    }
  ]
}
```

***

## 2. Create / Update Matcher

**Endpoints:**

* **Create:** `POST .../campaign/{id}/matchers`
* **Update:** `PUT .../campaign/{id}/matcher/{matcherId}`

### Request Attributes

| Attribute         | Type          | Description                                                                  |
| ----------------- | ------------- | ---------------------------------------------------------------------------- |
| **`name`**        | String        | **Required.** The display name of the matcher (e.g., "Cohen Family").        |
| **`description`** | String (HTML) | A short bio or dedication text. Supports HTML tags.                          |
| **`image`**       | String        | URL of the matcher's logo or photo. Use the [Media API](media.md) to upload. |
| **`goal`**        | Number        | The maximum amount they are matching (display purposes).                     |
| **`order`**       | Integer       | Sort order on the page. Lower numbers appear first.                          |
| **`active`**      | Boolean       | `true` to show on the page, `false` to hide default.                         |
| **`featured`**    | Boolean       | `true` to highlight this matcher (e.g., larger card or top of list).         |

### Example Request (Create)

```json
{
  "data": {
    "attributes": {
      "campaign_id": 45788,
      "name": "Silverman Brothers",
      "description": "<p>Dedicated in memory of our parents.</p>",
      "image": "https://cdn.charidy.com/images/silverman.jpg",
      "goal": 50000,
      "order": 2,
      "active": true,
      "featured": false
    }
  }
}
```

***

## 3. Delete Matcher

**Endpoint:** `DELETE .../campaign/{id}/matcher/{matcherId}`

**Response:**

```json
{
  "Result": "ok"
}
```
