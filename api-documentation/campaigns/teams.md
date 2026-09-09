# Teams & Ambassadors

The **Teams** API allows you to manage the fundraising teams (often called "Ambassadors" or "Groups") associated with a campaign.

Teams can have their own goals, pages (`slug`), and can be organized hierarchically (Groups -> Teams).

## Endpoints

| Method   | Endpoint & Description                                                                                                                                   |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`    | <p><code>/organization/{orgId}/campaign/{id}/teams</code><br><strong>Get Teams.</strong> Retrieve all teams with optional filters.</p>                   |
| `POST`   | <p><code>/organization/{orgId}/campaign/{id}/teams</code><br><strong>Create Team.</strong> Create a new team or group.</p>                               |
| `PUT`    | <p><code>/organization/{orgId}/campaign/{id}/team/{teamId}</code><br><strong>Update Team.</strong> Edit an existing team's details.</p>                  |
| `DELETE` | <p><code>/organization/{orgId}/campaign/{id}/team/{teamId}</code><br><strong>Delete Team.</strong> Remove a team.</p>                                    |
| `POST`   | <p><code>/organization/{orgId}/campaign/{id}/team/{teamId}/relations</code><br><strong>Relationships.</strong> Assign child teams to a parent group.</p> |

***

## 1. Get Teams

Retrieve a paginated list of teams. You can search by name, filter by hierarchy (parent ID), and include statistics.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/teams`

### Query Parameters

| Parameter        | Type       | Description                                                   |
| ---------------- | ---------- | ------------------------------------------------------------- |
| `page`           | `integer`  | Page number for pagination. Default: `1`.                     |
| `limit`          | `integer`  | Items per page. Default: `50`.                                |
| `q`              | `string`   | Search query (filters by team name).                          |
| `hidden`         | `boolean`  | Filter by visibility. `true` for hidden, `false` for visible. |
| `parent_team_id` | `integer`  | Filter by parent team ID. Use `0` for top-level teams/groups. |
| `extend`         | `string[]` | Pass `stats` to include `total_raised`, `donors_count`, etc.  |

### Response Dictionary

| Field                           | Type      | Description                                        |
| ------------------------------- | --------- | -------------------------------------------------- |
| `id`                            | `string`  | The unique ID of the team.                         |
| `attributes.name`               | `string`  | The display name of the team.                      |
| `attributes.slug`               | `string`  | The URL slug (e.g., `.../cmp/mycampaign/myteam`).  |
| `attributes.goal`               | `number`  | The fundraising goal amount.                       |
| `attributes.bonus_goal`         | `number`  | Secondary "bonus" goal amount.                     |
| `attributes.internal_goal`      | `number`  | Internal goal (not displayed publicly).            |
| `attributes.donor_goal`         | `integer` | Goal for number of donors.                         |
| `attributes.image`              | `string`  | URL to the team's profile image.                   |
| `attributes.leader_name`        | `string`  | Name of the team leader.                           |
| `attributes.leader_email`       | `string`  | Email of the team leader.                          |
| `attributes.phone`              | `string`  | Phone number of the team leader.                   |
| `attributes.parent_team_id`     | `integer` | The ID of the parent team (if this is a sub-team). |
| `attributes.description`        | `string`  | HTML content description of the team.              |
| `attributes.campaign_currrency` | `string`  | The currency code of the campaign (e.g. `USD`).    |
| `attributes.color`              | `string`  | Custom color hex code for the team page.           |
| `attributes.external_id`        | `string`  | External reference ID.                             |
| `attributes.hidden`             | `boolean` | Whether the team is hidden from public lists.      |
| `attributes.percentage_view`    | `number`  | Custom percentage override for display.            |
| `attributes.custom_data`        | `object`  | Custom metadata fields.                            |
| `attributes.custom_link`        | `string`  | Custom redirection link.                           |

```json
{
  "data": [
    {
      "type": "team",
      "id": "1449164",
      "attributes": {
        "name": "Team Name",
        "slug": "team-slug",
        "goal": 30000,
        "bonus_goal": 50000,
        "image": "https://cdn.charidy.com/...",
        "leader_name": "John Doe",
        "leader_email": "john@example.com",
        "phone": "555-0123",
        "parent_team_id": 0,
        "hidden": false
      }
    }
  ]
}
```

***

## 2. Create Team

Create a new team. To create a "Group" (a team that holds other teams), simply create a team that will later act as a parent.

**Endpoint:** `POST /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/teams`

### Request Attributes

| Attribute        | Type      | Required | Description                                                  |
| ---------------- | --------- | -------- | ------------------------------------------------------------ |
| `name`           | `string`  | **Yes**  | The name of the team.                                        |
| `slug`           | `string`  | **Yes**  | The unique URL suffix for the team page.                     |
| `goal`           | `integer` | No       | Fundraising goal amount.                                     |
| `bonus_goal`     | `integer` | No       | Secondary bonus goal.                                        |
| `donor_goal`     | `integer` | No       | Goal for number of donors.                                   |
| `leader_name`    | `string`  | No       | Name of the ambassador/leader.                               |
| `leader_email`   | `string`  | No       | Email address of the leader.                                 |
| `phone`          | `string`  | No       | Phone number of the leader.                                  |
| `image`          | `string`  | No       | URL of the profile image (upload via Media API first).       |
| `description`    | `string`  | No       | HTML content for the team page.                              |
| `parent_team_id` | `integer` | No       | ID of the parent team (if creating a sub-team). Default `0`. |
| `color`          | `string`  | No       | Hex color code (e.g., `#FF5733`).                            |
| `external_id`    | `string`  | No       | External reference ID.                                       |
| `hidden`         | `boolean` | No       | Set to `true` to hide the team.                              |
| `password`       | `string`  | No       | Optional password for team access.                           |

```json
{
  "data": {
    "attributes": {
      "name": "New York Community",
      "slug": "ny-community",
      "goal": 10000,
      "description": "<p>Join our local team to support the cause!</p>",
      "leader_name": "Michael Ross",
      "leader_email": "michael@example.com",
      "parent_team_id": 0,
      "hidden": false
    }
  }
}
```

***

## 3. Update Team

Modify an existing team's details.

**Endpoint:** `PUT /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/team/{teamId}`

Includes the same attributes as the **Create** endpoint.

```json
{
  "data": {
    "attributes": {
      "name": "Updated Team Name",
      "goal": 50000,
      "hidden": false
    }
  }
}
```

***

## 4. Delete Team

Permanently remove a team from the campaign.

**Endpoint:** `DELETE /orgarea/api/v1/organization/{orgId}/campaign/{campaignId}/team/{teamId}`

```json
{
  "Result": "ok"
}
```

***

## 5. Team Hierarchy (Relations)

Assign "Child Teams" to a "Parent Team" (Group). This is useful for structuring large campaigns with group leaders.

**Endpoint:** `POST /api/v1/organization/{orgId}/campaign/{campaignId}/team/{parentTeamId}/relations`

### Parameters

| Parameter               | Type        | Description                                          |
| ----------------------- | ----------- | ---------------------------------------------------- |
| `children_team_id_list` | `integer[]` | Array of Team IDs to become children of this parent. |

```json
{
  "children_team_id_list": [
    1449169,
    1449170
  ]
}
```
