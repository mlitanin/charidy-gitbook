# Design & Branding

Minimize the gap between your brand and your campaign page. This section covers templates and color schemes.

## 1. Templates

Templates control the overall layout and structural design of the campaign page. Unlike other settings, the template is controlled via a dedicated endpoint.

### Set Campaign Template

**Endpoint:** `POST /organization/{orgId}/campaign/{id}/template`

**Request Body:**

```json
{
  "data": {
    "attributes": {
      "code": "standard-v2"
    }
  }
}
```

**Common Template Codes:**

* `standard-v2`: The modern, default layout for standard crowdfunding campaigns.
* `unidy-v2`: Modern Unidy template with enhanced customization options.
* `giving-day`: Optimized for time-limited 24/36h campaigns.

***

## 2. Campaign Colors

The campaign's color palette is managed via the **Settings API** using a specific `meta_name`: **`campaign_colors`**.

## 1. Get Colors

**Endpoint:** `GET /campaign/{id}/setting?meta_name=campaign_colors`

**Response:** The colors are stored in the `meta_data` attribute as a **JSON string**.

```json
{
  "data": [
    {
      "type": "campaign_meta",
      "id": "1057328",
      "attributes": {
        "meta_name": "campaign_colors",
        "meta_data": "{\"colors\":{\"primary\":\"#cc0f0f\",\"secondary\":\"#bfacd2\"}}"
      }
    }
  ]
}
```

## 2. Update Colors

To set or change the colors, you update the `campaign_colors` setting.

**Endpoint:** `POST` or `PUT` `/campaign/{id}/setting`

**Request Body:**

You must construct a JSON object for the colors and then **stringify** it into the `meta_data` field.

**Color Structure (Pre-Stringify):**

```json
{
  "colors": {
    "primary": "#cc0f0f",   // Main button color, headers
    "secondary": "#bfacd2"  // Accents, backgrounds
  }
}
```

**Full Request:**

```json
{
  "data": {
    "type": "campaign_meta",
    "attributes": {
      "campaign_id": 45788,
      "meta_name": "campaign_colors",
      "meta_data": "{\"colors\":{\"primary\":\"#FF0000\",\"secondary\":\"#00FF00\"}}"
    }
  }
}
```

### Color Logic

* **Primary Color:** Used for the main "Donate" calls-to-action, progress bar fill (usually), and key headings.
* **Secondary Color:** Used for less prominent buttons, background accents, or secondary text highlights.
* **Defaults:** If this setting is not present, the campaign falls back to the organization's default brand colors or Charidy's system defaults.
