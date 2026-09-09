# Profile

Manage your organization's core identity, localization, and communication settings.

## Overview

The Organization Profile contains details visible to donors and used for system notifications. This includes the organization's name (in multiple languages), logo, contact emails, and short links.

## Endpoints

| Method  | Endpoint & Description                                                                                                                              |
| ------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PATCH` | <p><code>/organization/{orgId}/contact</code><br><strong>Update Profile.</strong> Update core details, translations, and notification settings.</p> |

***

## Update Organization Profile

Update the organization's public profile and internal settings. Note that the resource type is **`org_account`**.

**Endpoint:** `PATCH /orgarea/api/v1/organization/{orgId}/contact`

**Request Body:**

```json
{
  "data": {
    "type": "org_account",
    "id": "10001",
    "attributes": {
      "full_name": "Beit Elneama",
      "name": "Beit Elneama Org",
      "email": "contact@example.org",
      "extra_emails": [
        "finance@example.org",
        "support@example.org"
      ],
      "phone": "+972555555555",
      "about": "Helping the community...",
      "logo": "https://cdn.charidy.com/images/logo.png",
      "website": "https://example.org",
      "lang": "he",
      "timezone": "Asia/Jerusalem",
      "notifications_from_email": "noreply@example.org",
      "notifications_from_email_name": "Beit Elneama Notifications",
      "short_link": "my-org-link",
      "org_name_languages": [
        { "code": "he", "value": "שם בעברית" },
        { "code": "fr", "value": "Nom en français" }
      ],
      "primary": true
    },
    "relationships": {
      "customer_settings": {
        "data": [] 
      }
    }
  }
}
```

### Attributes

| Attribute                       | Type           | Description                                                     |
| ------------------------------- | -------------- | --------------------------------------------------------------- |
| `name`                          | String         | Official organization name (default language).                  |
| `full_name`                     | String         | Display name.                                                   |
| `email`                         | String         | Primary contact email.                                          |
| `extra_emails`                  | Array\<String> | Additional emails for notifications (e.g., billing, support).   |
| `org_name_languages`            | Array\<Object> | Localized names. Structure: `{ "code": "he", "value": "..." }`. |
| `short_link`                    | String         | Custom short link identifier for the organization.              |
| `phone`                         | String         | Primary contact phone.                                          |
| `logo`                          | String         | URL of the organization logo.                                   |
| `website`                       | String         | Organization's official website.                                |
| `about`                         | String         | Short bio/description.                                          |
| `lang`                          | String         | Default language code (e.g., `he`, `en`).                       |
| `timezone`                      | String         | Timezone (e.g., `Asia/Jerusalem`).                              |
| `notifications_from_email`      | String         | "From" address for system emails sent to donors.                |
| `notifications_from_email_name` | String         | "From" name for system emails sent to donors.                   |

***

## Localization Details

### Organization Name Translations

You can provide the organization's name in multiple languages using the `org_name_languages` attribute. This ensures donors see the name in their preferred language.

**Supported Language Codes:** `he`, `en`, `fr`, `es`, `ru`, etc.

**Example:**

```json
"org_name_languages": [
  { "code": "he", "value": "עמותת החסד" },
  { "code": "en", "value": "Charity Foundation" }
]
```
