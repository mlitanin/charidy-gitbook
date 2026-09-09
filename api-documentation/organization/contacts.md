# Contacts

Manage public and internal contacts for the organization. These contacts differ from "Users" in that they do not have login access to the dashboard. They are used for display purposes (e.g., on campaign pages) or for internal organization management (e.g., technical contact, donor support).

## Endpoints

| Method   | Endpoint & Description                                                                                                     |
| -------- | -------------------------------------------------------------------------------------------------------------------------- |
| `GET`    | <p><code>/organization/{orgId}/contacts</code><br><strong>List Contacts.</strong> Retrieve all organization contacts.</p>  |
| `POST`   | <p><code>/organization/{orgId}/contacts</code><br><strong>Create Contact.</strong> Add a new contact person.</p>           |
| `DELETE` | <p><code>/organization/{orgId}/contact/{contactId}</code><br><strong>Delete Contact.</strong> Remove a contact.</p>        |
| `GET`    | <p><code>/organization/{orgId}/contacts/types</code><br><strong>List Types.</strong> Get available contact role types.</p> |

***

## 1. List Contacts

Retrieve a list of all contacts associated with the organization.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/contacts`

**Response:**

```json
{
  "data": [
    {
      "type": "org_contact",
      "id": "123",
      "attributes": {
        "first_name": "John",
        "last_name": "Smith",
        "email": "support@example.com",
        "phone": "123-456-7890",
        "type": "donor_support",
        "note": "Available 9am-5pm EST",
        "created_at": 1678886400,
        "org_customer_id": "19763"
      }
    }
  ]
}
```

***

## 2. Create Contact

Add a new contact to the organization.

**Endpoint:** `POST /orgarea/api/v1/organization/{orgId}/contacts`

**Request Body:**

```json
{
  "data": {
    "attributes": {
      "first_name": "Jane",
      "last_name": "Doe",
      "type": "main",
      "phone": "058-123-4567",
      "email": "jane@example.com",
      "note": "Primary technical contact"
    }
  }
}
```

### Attributes

| Attribute    | Type   | Description                                                           |
| ------------ | ------ | --------------------------------------------------------------------- |
| `first_name` | String | **Required.** Contact's first name.                                   |
| `last_name`  | String | Contact's last name.                                                  |
| `type`       | String | **Required.** Role type (e.g., `main`, `technical`, `donor_support`). |
| `phone`      | String | Contact phone number.                                                 |
| `email`      | String | Contact email address.                                                |
| `note`       | String | Internal notes about the contact.                                     |

***

## 3. List Contact Types

Retrieve the valid types/roles that can be assigned to a contact.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/contacts/types`

**Response:**

```json
{
  "data": [
    "main",
    "technical",
    "donor_support",
    "accounting"
  ]
}
```

***

## 4. Delete Contact

Remove a contact from the organization.

**Endpoint:** `DELETE /orgarea/api/v1/organization/{orgId}/contact/{contactId}`

**Response:** `204 No Content`
