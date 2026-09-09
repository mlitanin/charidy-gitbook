# Media Handling

Understanding how to upload, manage, and attach images and videos across the Charidy API.

## Core Concept: "Upload & Link"

The Charidy API follows a consistent **"Upload & Link"** pattern for handling media assets:

1. **Upload:** You send the raw file to a dedicated upload endpoint.
2. **Get URL:** The API returns a public CDN URL (e.g., `https://cdn.charidy.com/...`).
3. **Link:** You send this URL as a string in the payload of another API call (e.g., updating an organization profile or adding a campaign image).

> **Tip:** Why this approach? This separates file storage concerns from resource management. It allows you to reuse the same image URL across multiple objects without re-uploading the file.

***

## 1. Uploading Files

There are two primary endpoints for uploading files, depending on the context.

## A. General Media Upload (`/account/media`)

Used for generic assets, primarily for **Campaign Media** and **Donation Levels**.

**Endpoint:** `POST /orgarea/api/v1/account/media` **Content-Type:** `multipart/form-data`

| Key    | Description                    |
| ------ | ------------------------------ |
| `file` | The binary file data (Max 2MB) |

**Response:**

```json
{
  "src": "https://cdn.charidy.com/images/123/example.jpg"
}
```

## B. Organization Image Upload (`/upload-image`)

Used specifically for **Legal Entity** assets (logos, signatures) and **Organization** logos.

**Endpoint:** `POST /api/organizations/{orgId}/upload-image` **Content-Type:** `multipart/form-data`

| Key    | Description          |
| ------ | -------------------- |
| `file` | The binary file data |

**Response:**

```json
{
  "url": "https://cdn.charidy.com/uploads/logo-123.png"
}
```

***

## 2. Linking Images to Objects

Once you have the URL (e.g., `src` or `url` from the upload response), you can attach it to various objects.

### Campaign Media

See [Campaign Media Documentation](../campaigns/media.md) for detailed instructions on attaching images with specific tags like `slider`, `shared_image`, etc.

### Legal Entities

Update the legal entity with the uploaded logo URL.

```json
/* PUT /api/organizations/{orgId}/legal-entities/{entityId} */
{
  "data": {
    "type": "org_legal_entity",
    "id": "17183",
    "attributes": {
      "receipt_logo": "https://cdn.charidy.com/uploads/logo-123.png"
    }
  }
}
```

## C. Donation Levels (Images)

Images for donation levels are also uploaded via `/account/media` (same as campaign media), and the resulting URL is linked to the level.

**1. Upload Image:** `POST /orgarea/api/v1/account/media` -> Returns `{"src": "https://..."}`

**2. Link to Level:**

```json
/* POST /api/v1/organization/{orgId}/campaign/{campId}/donation_levels */
{
  "data": {
    "type": "donation_level",
    "attributes": {
      "amount": 100,
      "description": "Silver Sponsor",
      "image": "https://cdn.charidy.com/images/loginid123/example.jpg"
    }
  }
}
```

***

## D. Matchers & Teams (Images)

Both Matchers (Matching Donors) and Teams (Ambassadors) use the same standard flow.

**1. Upload Image:** `POST /orgarea/api/v1/account/media` -> Returns `{"src": "https://..."}`

**2. Link to Matcher:**

```json
/* POST /api/v1/organization/{orgId}/campaign/{campId}/matchers */
{
  "data": {
    "type": "matcher",
    "attributes": {
      "name": "Generous Donor",
      "image": "https://cdn.charidy.com/images/loginid123/matcher.jpg"
    }
  }
}
```

**3. Link to Team:**

```json
/* POST /api/v1/organization/{orgId}/campaign/{campId}/teams */
{
  "data": {
    "type": "team",
    "attributes": {
      "name": "Team Aleph",
      "image": "https://cdn.charidy.com/images/loginid123/team.jpg"
    }
  }
}
```

***

## Video Assets

Unlike images, video files (MP4, MOV) are **not** uploaded directly to the Charidy API. instead, you must host them on external platforms like **YouTube** or **Vimeo** and use their public links.

**Supported Platforms:**

* **YouTube:** `https://www.youtube.com/watch?v=VIDEO_ID` or `https://youtu.be/VIDEO_ID`
* **Vimeo:** `https://vimeo.com/VIDEO_ID`

**Usage:** Add the video URL as the `src` value when creating a media object with the `video` or `countdown_video` tag.

```json
{
  "data": {
    "attributes": {
      "tag": "video",
      "src": "https://youtu.be/example123"
    }
  }
}
```
