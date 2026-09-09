# Users

Manage organization team members and their dashboard access levels.

## Endpoints

| Method  | Endpoint & Description                                                                                                                                 |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `GET`   | <p><code>/organization/{orgId}/accounts</code><br><strong>List Users.</strong> Retrieve all users with access to the organization.</p>                 |
| `POST`  | <p><code>/organization/{orgId}/accounts</code><br><strong>Invite User.</strong> Create a new sub-account and send an email invitation.</p>             |
| `PATCH` | <p><code>/organization/{orgId}/account/{accountId}</code><br><strong>Update User.</strong> Modify access roles, permissions, or details.</p>           |
| `PATCH` | <p><code>/organization/{orgId}/account/{accountId}</code><br><strong>Deactivate.</strong> Use the update endpoint with <code>active: false</code>.</p> |

***

## 1. List Users

Retrieve a list of all users who have access to the organization's dashboard.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/accounts`

**Response:**

```json
{
  "data": [
    {
      "type": "login_to_org",
      "id": "16274",
      "attributes": {
        "access": "full",
        "email": "user@example.com",
        "first_name": "John",
        "last_name": "Doe",
        "active": true,
        "avatar": "https://cdn.charidy.com/images/123/avatar.png",
        "acl_list": [
          "CanSeeCampaignDashboard",
          "CanEditCampaignDetails"
        ]
      }
    }
  ]
}
```

***

## 2. Invite User (Create)

Create a new user account. **This action automatically sends an invitation email.**

You can assign a broad role (`access`) and fine-tune permissions using the Access Control List (ACL). ACL items can be global or restricted to specific campaigns.

**Endpoint:** `POST /orgarea/api/v1/organization/{orgId}/accounts`

**Request Body (Basic Role):**

```json
{
  "data": {
    "attributes": {
      "email": "teammember@example.com",
      "first_name": "Team",
      "last_name": "Member",
      "access": "full",
      "avatar": "https://cdn.charidy.com/uploads/avatar.png",
      "active": true
    },
    "relationships": {
      "restricted_acl_items": { "data": [] }
    }
  }
}
```

**Request Body (With Specific Campaign Permissions):** Use this to give a "Restricted" user access only to specific actions on specific campaigns.

```json
{
  "data": {
    "attributes": {
      "email": "restricted@example.com",
      "first_name": "Limited",
      "last_name": "User",
      "access": "restricted",
      "active": true
    },
    "relationships": {
      "restricted_acl_items": {
        "data": [
          { "type": "restricted_acl_item", "id": "0" },
          { "type": "restricted_acl_item", "id": "1" }
        ]
      }
    }
  },
  "included": [
    {
      "type": "restricted_acl_item",
      "id": "0",
      "attributes": {
        "action": "CanEditCampaignDetails",
        "campaign_id": 45788 
      }
    },
    {
      "type": "restricted_acl_item",
      "id": "1",
      "attributes": {
        "action": "CanSeeCampaignDashboard",
        "campaign_id": 0
      }
    }
  ]
}
```

> **Tip:** Campaign ID Logic

* `campaign_id: 0`: The permission applies to **All Campaigns** (Global).
* `campaign_id: 45788`: The permission applies **only** to campaign #45788.

***

## 3. Update User

**Endpoint:** `PATCH /orgarea/api/v1/organization/{orgId}/account/{accountId}`

**Request Body:**

```json
{
  "data": {
    "id": "16275",
    "type": "login_to_org",
    "attributes": {
      "access": "restricted"
    },
    "relationships": {
      "restricted_acl_items": {
        "data": [
          { "type": "restricted_acl_item", "id": "0" }
        ]
      }
    }
  },
  "included": [
    {
      "type": "restricted_acl_item",
      "id": "0",
      "attributes": {
        "action": "CanSeeCampaignDashboard",
        "campaign_id": 45788
      }
    }
  ]
}
```

### Deactivate User

To remove access without deleting the user record:

```json
{
  "data": {
    "attributes": {
      "active": false
    }
  }
}
```

***

## Reference Lists

### Roles Reference

| Role Key     | Description              | Capabilities                                                        |
| ------------ | ------------------------ | ------------------------------------------------------------------- |
| `full`       | **Full access**          | Global admin access to organization and all campaigns.              |
| `operator`   | **Operation Room Agent** | Can edit donations and teams. Useful for call centers.              |
| `team_agent` | **Team agent**           | Can moderate teams.                                                 |
| `restricted` | **Restricted**           | Base access is empty; specific permissions must be granted via ACL. |

### ACL Permissions

These actions can be assigned in `restricted_acl_items`.

| Permission Key                     | Description                      |
| ---------------------------------- | -------------------------------- |
| `CanSeeCampaignDashboard`          | View campaign statistics.        |
| `CanCreateCampaign`                | Create new campaigns.            |
| `CanEditCampaignDetails`           | Edit goals, descriptions, dates. |
| `CanEditCampaignMedia`             | Manage images/video.             |
| `CanEditCampaignMatchers`          | Manage matchers.                 |
| `CanEditCampaignDonationLevels`    | Manage donation amounts.         |
| `CanSeeCampaignTeams`              | View teams.                      |
| `CanAddCampaignTeams`              | Create teams.                    |
| `CanEditCampaignTeams`             | Edit teams.                      |
| `CanDeleteAllCampaignTeams`        | Delete teams.                    |
| `CanEditCampaignDonation`          | Edit donation records.           |
| `CreateOfflineCampaignDonation`    | add manual offline donations.    |
| `CanImportOfflineCampaignDonation` | Bulk import donations.           |
| `CanSeeOrganizationContactList`    | View user list.                  |
| `CanEditOrganizationContactList`   | Manage users.                    |
| `CanSeeOrganizationGatewayList`    | View gateways.                   |
| `CanEditOrganizationGatewayList`   | Configure gateways.              |
| `CanSeeAllOrgDonationList`         | View global donation list.       |
