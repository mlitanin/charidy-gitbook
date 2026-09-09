# Overview

This section covers the management of the **Campaign Object**. A campaign is the central entity in the fundraising platform, connecting donations, teams, media, and stories.

Here you will find documentation for:

* **Creating & Updating** the campaign entity itself.
* **Retrieving** campaign data and its related resources.
* Managing **Sub-resources** like Teams, Media, and Content.

## Key Concepts

* **Campaign ID**: The unique identifier for each campaign (e.g., `45788`).
* **Organization ID**: The ID of the organization that owns the campaign (e.g., `19763`).
* **Attributes**: Core fields like `title`, `goal`, `currency`, `start_date`, and `end_date`.
* **Relationships**: Links to other resources like `organization`, `teams`, `matchers`, and `media`.

## Available Endpoints

| Method | Endpoint & Description                                                                                                                                    |
| ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`  | <p><code>/organization/{orgId}/campaign/{campaignId}</code><br><strong>Get Campaign.</strong> Retrieve full campaign details.</p>                         |
| `PUT`  | <p><code>/organization/{orgId}/campaign/{campaignId}</code><br><strong>Update Campaign.</strong> Update an existing campaign.</p>                         |
| `POST` | <p><code>/organization/{orgId}/campaign</code><br><strong>Create Campaign.</strong> Create a new campaign.</p>                                            |
| `GET`  | <p><code>/organization/{orgId}/campaign/{campaignId}/setting</code><br><strong>Get Settings.</strong> Retrieve specific campaign settings (metadata).</p> |
| `PUT`  | <p><code>/organization/{orgId}/campaign/{campaignId}/setting</code><br><strong>Update Settings.</strong> Update campaign settings.</p>                    |
