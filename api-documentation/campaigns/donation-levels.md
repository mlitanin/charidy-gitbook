# Donation Levels

Manage the predefined donation amounts (suggested giving levels) for a campaign. Users can select one of these levels or enter a custom amount.

Levels can be customized with images, descriptions, and language-specific overrides (e.g., different titles or images for English vs. Hebrew).

## Endpoints

| Method   | Endpoint & Description                                                                                                                                                             |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`    | <p><code>/organization/{orgId}/campaign/{id}?extend=donation_levels</code><br><strong>Get Levels.</strong> Retrieved associated levels as part of the Campaign Object.</p>         |
| `POST`   | <p><code>/organization/{orgId}/campaign/{id}/donation_levels</code><br><strong>Create Level.</strong> Add a new donation option to the campaign.</p>                               |
| `PUT`    | <p><code>/organization/{orgId}/campaign/{id}/donation_level/{levelId}</code><br><strong>Update Level.</strong> Modify details like amount, image, translations, or visibility.</p> |
| `DELETE` | <p><code>/organization/{orgId}/campaign/{id}/donation_level/{levelId}</code><br><strong>Delete Level.</strong> Remove a donation option permanently.</p>                           |

***

## 1. Get Donation Levels

Donation levels are not fetched via a standalone endpoint but are **included** in the Campaign response when requesting `extend=donation_levels`.

**Endpoint:** `GET /api/v1/organization/{orgId}/campaign/{id}?extend=donation_levels`

**Response Structure:** The levels appear in the `included` array. Each object contains the base attributes and a stringified `meta` JSON for advanced configurations.

```json
{
  "data": {
    "type": "campaign",
    "id": "45788",
    "relationships": {
      "donation_level": {
        "data": [
          { "type": "donation_level", "id": "80142" }
        ]
      }
    }
  },
  "included": [
    {
      "type": "donation_level",
      "id": "80142",
      "attributes": {
        "amount": 600,
        "title": "Platinum Sponsor",
        "subtitle": "Provide meals for a month",
        "currency": "ils",
        "order": 1,
        "image": "https://cdn.charidy.com/images/platinum-badge.png",
        "color": "#a6afc4",
        "limited": 0,
        "team_id": 0,
        "meta": "{\"amount\":{\"usd\":189},\"title_loc\":{\"he\":\"שותף פלטינום\"}, \"hidden_on_campaign_page\": false}"
      }
    }
  ]
}
```

***

## 2. Create / Update Level

Use `POST` to create and `PUT` to update. The structure is identical.

**Endpoints:**

* **Create:** `POST .../campaign/{id}/donation_levels`
* **Update:** `PUT .../campaign/{id}/donation_level/{levelId}`

### Request Attributes

| Attribute      | Type        | Description                                                                                               |
| -------------- | ----------- | --------------------------------------------------------------------------------------------------------- |
| **`amount`**   | Number      | **Required.** The specific donation amount in the campaign's base currency.                               |
| **`title`**    | String      | The main label displayed on the button.                                                                   |
| **`subtitle`** | String      | Smaller text below the title.                                                                             |
| **`image`**    | String      | Full URL of the image. **Note:** Upload the image first using the [Media API](media.md) to get a CDN URL. |
| **`color`**    | String      | Hex color code (e.g., `#213b7f`) for the button background.                                               |
| **`order`**    | Integer     | Sort order. Lower numbers appear first.                                                                   |
| **`limited`**  | Integer     | Set a limit on how many times this level can be selected (0 = unlimited).                                 |
| **`team_id`**  | Integer     | Assign to a specific team (0 = global).                                                                   |
| **`meta`**     | JSON String | **Critical.** controls translations, multicurrency, and display logic. See below.                         |

### ⚠️ The Meta Field (Critical)

The `meta` field is the most important part of the donation level configuration. It holds all the advanced logic.

**Important Rule:** The API expects this field to be a **String**, not an Object. You must use `JSON.stringify()` on your client before sending it.

**Meta Structure (Before Stringify):**

```json
{
  // 1. Currency Overrides
  // Define exact amounts for other currencies.
  // NOTE: The frontend reads these values directly to handle "instant" currency switching.
  // There is no separate API call for converting levels.
  "amount": {
    "usd": 180,
    "gbp": 140,
    "eur": 160
  },

  // 2. Localization (Translations)
  // Map language codes (he, fr, es) to translated strings.
  "title_loc": {
    "he": "שותף זהב",
    "fr": "Partenaire Gold"
  },
  "subtitle_loc": {
    "he": "תרום עכשיו",
    "fr": "Faites un don"
  },
  "image_loc": {
    "he": "https://cdn.charidy.com/images/hebrew-badge.png"
  },

  // 3. Logic & Behavior
  "hidden_on_campaign_page": false, // Hide from public view?
  "installment": false,             // Is this a recurring payment plan?
  "show_count_donations": true,     // Show "15 people chose this"?
  "tooltip_description": "Tax deductible donation",
  "skip_multistep": false
}
```

### Example Request (Create)

Notice how the `meta` field is escaped and passed as a string string.

```json
{
  "data": {
    "attributes": {
      "campaign_id": 45788,
      "amount": 360,
      "title": "Chai Sponsor",
      "subtitle": "Life saving donation",
      "color": "#213b7f",
      "order": 1,
      "image": "https://cdn.charidy.com/images/chai-badge.png",
      "meta": "{\"amount\":{\"usd\":100},\"title_loc\":{\"he\":\"שותף חי\"},\"subtitle_loc\":{\"he\":\"תרומה מצילת חיים\"}}"
    }
  }
}
```

***

## 3. Delete Level

**Endpoint:** `DELETE .../campaign/{id}/donation_level/{levelId}`

**Response:**

```json
{
  "Result": "ok"
}
```
