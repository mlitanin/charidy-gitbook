# Overview

Centralized management for all organization media assets. This system allows you to assign specific images or videos to campaigns, teams, languages, and display locations (tags).

## Endpoints

| Method   | Endpoint & Description                                                                                                                                                    |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`    | <p><code>/organization/{orgId}/campaign/{id}?extend=media</code><br><strong>List Media.</strong> Retrieve all media assignments via the campaign object.</p>              |
| `POST`   | <p><code>/organization/{orgId}/campaign/{campaignId}/media</code><br><strong>Create Media.</strong> Assign an uploaded asset to a specific slot.</p>                      |
| `PUT`    | <p><code>/organization/{orgId}/campaign/{campaignId}/media/{mediaId}</code><br><strong>Update Media.</strong> Modify the assignment (e.g., change language or order).</p> |
| `DELETE` | <p><code>/organization/{orgId}/campaign/{campaignId}/media/{mediaId}</code><br><strong>Delete Media.</strong> Remove the assignment.</p>                                  |

***

## 1. List Media Assignments

There is no dedicated endpoint to list media. Instead, you must request the campaign resource and extend it with the `media` relationship.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}`

**Query Parameters:**

| Parameter | Value   | Description                                        |
| --------- | ------- | -------------------------------------------------- |
| `extend`  | `media` | Includes the `media` relationship in the response. |

**Response Snippet:**

```json
{
  "data": {
    "type": "campaign",
    "id": "45788",
    "relationships": {
      "media": {
        "data": [
          { "type": "media", "id": "365024" },
          { "type": "media", "id": "365025" }
        ]
      }
    }
  },
  "included": [
    {
      "type": "media",
      "id": "365024",
      "attributes": {
        "campaign_id": 45788,
        "src": "https://cdn.charidy.com/images/123/banner.jpg",
        "tag": "slider",
        "lang": "he",
        "team_id": 0,
        "order": 1
      }
    },
    {
      "type": "media",
      "id": "365025",
      "attributes": {
        "campaign_id": 45788,
        "src": "https://cdn.charidy.com/images/123/mobile-hero.jpg",
        "tag": "campaign_hero_mobile",
        "lang": "he",
        "team_id": 1449278
      }
    }
  ]
}
```

***

## 2. Create Media Assignment

After uploading an image (via `/account/media`), use this endpoint to assign it to a specific context (Campaign, Team, Language, Tag).

**Endpoint:** `POST /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/media`

**Request Body:**

```json
{
  "data": {
    "attributes": {
      "campaign_id": 45788,
      "src": "https://cdn.charidy.com/images/123/banner.jpg",
      "tag": "campaign_hero",
      "lang": "en",
      "team_id": 0,
      "order": 1
    }
  }
}
```

### Attributes

| Attribute     | Type    | Description                                                                             |
| ------------- | ------- | --------------------------------------------------------------------------------------- |
| `src`         | String  | **Required.** The URL of the uploaded image/video.                                      |
| `tag`         | String  | **Required.** The display location key (see [Media Tags](media-storage.md#media-tags)). |
| `lang`        | String  | Language code (e.g., `en`, `he`). Leave empty for all languages.                        |
| `team_id`     | Integer | ID of the team to assign this media to. Use `0` for campaign-level media.               |
| `order`       | Integer | Display order (for sliders/lists).                                                      |
| `campaign_id` | Integer | The campaign ID context.                                                                |

***

## 3. Update Media Assignment

**Endpoint:** `PUT /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/media/{mediaId}`

**Request Body:**

```json
{
  "data": {
    "type": "media",
    "id": "365024",
    "attributes": {
      "order": 2,
      "lang": "he"
    }
  }
}
```

***

## 4. Delete Media Assignment

**Endpoint:** `DELETE /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/media/{mediaId}`

***

## Reference Lists

### Media Tags

Determines where the media asset appears in the UI.

| Tag Key                 | Description                                    |
| ----------------------- | ---------------------------------------------- |
| `countdown_hero`        | Countdown page image (desktop)                 |
| `countdown_hero_mobile` | Countdown page image (mobile)                  |
| `countdown_video`       | Countdown Video                                |
| `homepage`              | Campaign image in the live list                |
| `slider`                | Regular campaign slider                        |
| `video`                 | Regular campaign video                         |
| `campaign_hero`         | Campaign page slider (desktop)                 |
| `campaign_hero_mobile`  | Campaign page slider (mobile)                  |
| `shared_image`          | Campaign page share image (Facebook, WhatsApp) |
| `projector_banner`      | Projector Mode Banner                          |
| `team_default`          | Team page slider                               |
| `team_default_avatar`   | Team page default avatar                       |
| `brand_slider`          | Brand slider                                   |
| `footer_image`          | Campaign footer image (desktop)                |
| `footer_image_mobile`   | Campaign footer image (mobile)                 |

#### Campaign Page V2 Specific

| Tag Key                              | Description                          |
| ------------------------------------ | ------------------------------------ |
| `hero_countdown_desktop`             | Hero countdown (desktop)             |
| `hero_countdown_mobile`              | Hero countdown (mobile)              |
| `hero_live_desktop`                  | Hero live (desktop)                  |
| `hero_live_mobile`                   | Hero live (mobile)                   |
| `hero_complete_desktop`              | Hero complete (desktop)              |
| `hero_complete_mobile`               | Hero complete (mobile)               |
| `slider_countdown_desktop`           | Slider countdown (desktop)           |
| `slider_countdown_mobile`            | Slider countdown (mobile)            |
| `slider_live_desktop`                | Slider live (desktop)                |
| `slider_live_mobile`                 | Slider live (mobile)                 |
| `slider_complete_desktop`            | Slider complete (desktop)            |
| `slider_complete_mobile`             | Slider complete (mobile)             |
| `past_achievements`                  | Past Achievements section            |
| `storyfest_section`                  | Storyfest section                    |
| `live_feed_section`                  | Live feed section                    |
| `team_feed_section_video_horizontal` | Team feed section video (horizontal) |
| `team_feed_section_video_vertical`   | Team feed section video (vertical)   |

### Supported Languages

Use these codes in the `lang` field.

| Code    | Language               |
| ------- | ---------------------- |
| `en`    | English                |
| `he`    | Hebrew (עברית)         |
| `fr`    | French (Français)      |
| `ru`    | Russian (Pу́сский)     |
| `es`    | Spanish (Español)      |
| `pt`    | Portuguese (Português) |
| `it`    | Italian (Italiano)     |
| `de`    | German (Deutsch)       |
| `pl`    | Polish (Polski)        |
| `fi`    | Finnish                |
| `et`    | Estonian               |
| `az`    | Azerbaijan             |
| `sv`    | Swedish                |
| `nl`    | Dutch                  |
| `da`    | Danish                 |
| `lk`    | לשון הקודש             |
| `hu`    | Hungarian              |
| `ar`    | Arabic                 |
| `yi`    | Yiddish                |
| `ro`    | Romanian               |
| `zh`    | Chinese                |
| `no`    | Norwegian              |
| `hi`    | Indian                 |
| `el`    | Greek                  |
| `en-gb` | English UK             |
| `ua`    | Ukrainian              |
| `pt-br` | Portuguese (Brazilian) |
