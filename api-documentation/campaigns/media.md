# Media

Manage all visual assets for the campaign, including main videos, countdown videos, desktop banners, mobile heroes, and social sharing images.

Media management uses a robust tagging system to determine where each asset appears (Desktop vs. Mobile, Standard vs. Countdown) and supports language-specific overrides.

## Endpoints

| Method   | Endpoint & Description                                                                                                                                     |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `POST`   | <p><code>/account/media</code><br><strong>Upload Media.</strong> Upload a raw file to the CDN. Returns a URL.</p>                                          |
| `POST`   | <p><code>/organization/{orgId}/campaign/{id}/media</code><br><strong>Attach Media.</strong> Link the uploaded URL to the campaign with a specific tag.</p> |
| `PUT`    | <p><code>/organization/{orgId}/campaign/{id}/media/{mediaId}</code><br><strong>Update Media.</strong> Change language, order, or image source.</p>         |
| `DELETE` | <p><code>/organization/{orgId}/campaign/{id}/media/{mediaId}</code><br><strong>Delete Media.</strong> Remove a media asset.</p>                            |

***

## Media Tags & Logic

Use these "Smart Tags" to control where your images/videos appear.

| Tag                         | Context           | Description                                                       |
| --------------------------- | ----------------- | ----------------------------------------------------------------- |
| **`video`**                 | Main Campaign     | The primary video shown on the campaign page (YouTube/Vimeo URL). |
| **`countdown_video`**       | Countdown         | Video shown during the countdown phase (YouTube/Vimeo URL).       |
| **`slider`**                | Standard Desktop  | Images for the main desktop carousel.                             |
| **`campaign_hero_mobile`**  | Standard Mobile   | Specific mobile-optimized images (Standard mode).                 |
| **`countdown_hero`**        | Countdown Desktop | Banner/Slider for the countdown page (Desktop).                   |
| **`countdown_hero_mobile`** | Countdown Mobile  | Specific mobile-optimized images (Countdown mode).                |
| **`shared_image`**          | Social Media      | The OG Image used for Facebook/WhatsApp sharing.                  |

> **Info:** Video URLs For `video` and `countdown_video` tags, use YouTube or Vimeo URLs:

* **YouTube:** `https://youtu.be/VIDEO_ID` or `https://www.youtube.com/watch?v=VIDEO_ID`
* **Vimeo:** `https://vimeo.com/VIDEO_ID`

The platform automatically embeds the video player on the campaign page.

> **Tip:** Device Optimization Always upload separate images for Desktop (`slider`) and Mobile (`campaign_hero_mobile`) to ensure the best user experience on all devices.

***

## Recommended Image Sizes

For optimal display quality and performance, follow these size guidelines:

### Desktop Images (`slider`, `countdown_hero`)

* **Recommended Size:** 1920px × 450px
* **Maximum File Size:** 2 MB
* **Format:** JPG, PNG, WebP
* **Aspect Ratio:** \~4.27:1 (wide banner)

### Mobile Images (`campaign_hero_mobile`, `countdown_hero_mobile`)

* **Recommended Size:** 1024px × 576px
* **Maximum File Size:** 2 MB
* **Format:** JPG, PNG, WebP
* **Aspect Ratio:** 16:9

### Social Share Images (`shared_image`)

* **Recommended Size:** 1200px × 630px
* **Maximum File Size:** 300 KB (for fast loading)
* **Format:** JPG, PNG
* **Aspect Ratio:** 1.91:1 (Facebook/WhatsApp OG standard)

> **Warning:** File Size Matters Large images slow down page load times. Always optimize images before uploading:

* Use compression tools (TinyPNG, ImageOptim, etc.)
* Choose the right format (JPG for photos, PNG for graphics with transparency)
* Consider WebP for modern browsers (smaller file size, same quality)

> **Tip:** Refresh Facebook/WhatsApp Cache After uploading a new `shared_image`, use the "Refresh Facebook/WhatsApp Cache" button in the dashboard (or call the Facebook Graph API directly) to ensure the new image appears immediately when sharing.

***

## 1. Upload & Attach Media

Adding an image is a two-step process: First, upload the physical file, then attach it to the campaign logic.

## 1. Upload File

Upload the file to the global media storage.

**Endpoint:** `POST https://dashboardapi.charidy.com/orgarea/api/v1/account/media` **Type:** `multipart/form-data`

| Key    | Value         |
| ------ | ------------- |
| `file` | (Binary File) |

**Response:**

```json
{
  "src": "https://cdn.charidy.com/images/loginid123/example_345.png"
}
```

## 2. Attach Media

Use the `src` from Step 1 to create a Campaign Media object.

**Endpoint:** `POST /api/v1/organization/{orgId}/campaign/{campaignId}/media`

```json
{
  "data": {
    "attributes": {
      "src": "https://cdn.charidy.com/images/loginid123/example_345.png",
      "tag": "slider",
      "lang": "", 
      "order": 1
    }
  }
}
```

* **`lang`**: Set to `"en"`, `"he"`, etc., for language-specific images. Leave empty `""` for the default image.
* **`order`**: Integer to control the position in the slider.

***

## 2. Manage Media

### Update Media

Modify attributes like sorting order or language assignment.

**Endpoint:** `PUT /api/v1/organization/{orgId}/campaign/{campaignId}/media/{mediaId}`

```json
{
  "data": {
    "attributes": {
      "campaign_id": 45788,
      "src": "...",
      "tag": "slider",
      "lang": "he",
      "order": 2
    }
  }
}
```

## 3. Delete Media

**Endpoint:** `DELETE /api/v1/organization/{orgId}/campaign/{campaignId}/media/{mediaId}`

***

## 3. Social Sharing & Cache (Reference)

The `shared_image` tag sets the Open Graph (OG) image for Facebook and WhatsApp. When you update this image, it is **highly recommended** to force Facebook to re-scrape the campaign URL to ensure the new image appears immediately.

This action involves calling the **Facebook Graph API** directly, creating a "Scrape" request fueled by Charidy campaign data.

### Force Scrape (Client-Side Action)

**Endpoint:** `POST https://graph.facebook.com`

**Query Parameters:**

| Parameter      | Value               | Description                                                                            |
| -------------- | ------------------- | -------------------------------------------------------------------------------------- |
| `id`           | `{CAMPAIGN_URL}`    | The full URL of the Charidy campaign (e.g., `https://www.charidy.com/cmp/mycampaign`). |
| `scrape`       | `true`              | Forces a fresh scrape of the OG tags.                                                  |
| `access_token` | `{FB_ACCESS_TOKEN}` | A valid Facebook App Access Token or Client Token.                                     |

```bash
curl -X POST \
  "https://graph.facebook.com/?id=https://www.charidy.com/cmp/my-campaign&scrape=true&access_token=YOUR_TOKEN"
```

This is useful for automation or if you are building a custom dashboard and want to provide a "Refresh Preview" button similar to the official Charidy Dashboard.
