# Organizations

Manage organizations and select which organization to work with when a user has access to multiple organizations.

## Overview

After successful authentication, users may have access to one or more organizations. Each organization represents a separate entity with its own:

* Campaigns
* Donations
* Settings
* Legal entities
* Payment gateways
* Users

> **Tip:** If a user belongs to multiple organizations, they must select which one to work with. The selected organization ID is used in all subsequent API calls (e.g., `/organization/{orgId}/campaigns`).

***

## Endpoints

| Method | Endpoint & Description                                                                                                                       |
| ------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`  | <p><code>/organizations</code><br><strong>List Organizations.</strong> Get all organizations the user has access to.</p>                     |
| `GET`  | <p><code>/organization/{orgId}</code><br><strong>Get Organization.</strong> Retrieve detailed information about a specific organization.</p> |

***

## 1. List Organizations

After successful login, retrieve the list of organizations the user has access to.

**Endpoint:** `GET /orgarea/api/v1/organizations`

**Response:**

```json
{
  "data": [
    {
      "type": "org_account",
      "id": "10001",
      "attributes": {
        "name": "My Organization",
        "full_name": "John Doe",
        "email": "admin@example.com",
        "phone": "+1 555 123 4567",
        "lang": "en",
        "timezone": "America/New_York",
        "logo": "https://cdn.charidy.com/logos/org-logo.png",
        "short_link": "--1090",
        "short_url": "",
        "website": "https://example.org",
        "about": "Organization description",
        "primary": true,
        "extra_emails": [],
        "org_name_languages": [],
        "notifications_from_email": "notifications@example.org",
        "notifications_from_email_name": "My Organization"
      }
    },
    {
      "type": "org_account",
      "id": "10002",
      "attributes": {
        "name": "Second Organization",
        "full_name": "John Doe",
        "email": "admin@example2.com",
        "phone": "+1 555 987 6543",
        "lang": "he",
        "timezone": "Asia/Jerusalem",
        "logo": "https://cdn.charidy.com/logos/org2-logo.png",
        "primary": false,
        "website": "https://example2.org",
        "about": "Second organization description"
      }
    }
  ]
}
```

### Organization Attributes

| Attribute                       | Type    | Description                                                                |
| ------------------------------- | ------- | -------------------------------------------------------------------------- |
| `id`                            | String  | **Organization ID** (use this in API paths like `/organization/{id}/...`). |
| `name`                          | String  | Organization name.                                                         |
| `full_name`                     | String  | Full name of the organization administrator.                               |
| `email`                         | String  | Primary organization email.                                                |
| `phone`                         | String  | Organization phone number.                                                 |
| `lang`                          | String  | Default language code (e.g., `en`, `he`, `fr`).                            |
| `timezone`                      | String  | Organization timezone (e.g., `America/New_York`, `Asia/Jerusalem`).        |
| `logo`                          | String  | URL to organization logo.                                                  |
| `short_link`                    | String  | Short link identifier.                                                     |
| `short_url`                     | String  | Short URL for the organization.                                            |
| `website`                       | String  | Organization website URL.                                                  |
| `about`                         | String  | Organization description.                                                  |
| `primary`                       | Boolean | Whether this is the user's primary organization.                           |
| `extra_emails`                  | Array   | Additional email addresses for the organization.                           |
| `org_name_languages`            | Array   | Organization name translations in different languages.                     |
| `notifications_from_email`      | String  | Email address used for sending notifications.                              |
| `notifications_from_email_name` | String  | Display name for notification emails.                                      |

***

## 2. Get Organization Details

Retrieve detailed information about a specific organization, including settings.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}?extend=settings`

**Query Parameters:**

* `extend` - Optional. Include related data (e.g., `settings`)

**Response:**

```json
{
  "data": {
    "type": "org_account",
    "id": "10001",
    "attributes": {
      "name": "My Organization",
      "full_name": "John Doe",
      "email": "admin@example.org",
      "phone": "+1 555 123 4567",
      "lang": "he",
      "timezone": "Asia/Jerusalem",
      "logo": "https://cdn.charidy.com/logos/logo.png",
      "website": "https://example.org",
      "about": "Organization description",
      "primary": true,
      "extra_emails": ["support@example.org", "info@example.org"],
      "notifications_from_email": "notifications@example.org",
      "notifications_from_email_name": "My Organization"
    },
    "relationships": {
      "customer_settings": {
        "data": [
          {
            "type": "customer_setting",
            "id": "7"
          }
        ]
      }
    }
  },
  "included": [
    {
      "type": "customer_setting",
      "id": "7",
      "attributes": {
        "type": "org_public_page",
        "val_bool": false
      }
    }
  ]
}
```

### Extended Data

When using `?extend=settings`, the response includes:

**Relationships:**

* `customer_settings` - Organization-specific settings

**Included:**

* Settings objects with type and values

***

## Use Cases

### Select Organization (Multi-Org Users)

```javascript
// Step 1: Get all organizations
const response = await fetch('/orgarea/api/v1/organizations', {
  headers: { 'Authorization': `Bearer ${jwt_token}` }
});

const { data } = await response.json();

// Step 2: Check if user has multiple organizations
if (data.length === 1) {
  // Single organization - use it automatically
  const orgId = data[0].id;
  console.log(`Using organization: ${data[0].attributes.name}`);
  
} else if (data.length > 1) {
  // Multiple organizations - let user choose
  console.log('Select an organization:');
  data.forEach((org, index) => {
    const isPrimary = org.attributes.primary ? ' (Primary)' : '';
    console.log(`${index + 1}. ${org.attributes.name}${isPrimary}`);
  });
  
  // After user selects, store the organization ID
  const selectedOrg = data[userSelection];
  const orgId = selectedOrg.id;
  
  // Save for subsequent API calls
  localStorage.setItem('selected_org_id', orgId);
  localStorage.setItem('selected_org_name', selectedOrg.attributes.name);
}
```

### Get Primary Organization

```javascript
async function getPrimaryOrganization() {
  const response = await fetch('/orgarea/api/v1/organizations', {
    headers: { 'Authorization': `Bearer ${token}` }
  });
  
  const { data } = await response.json();
  
  // Find primary organization
  const primary = data.find(org => org.attributes.primary);
  
  return primary || data[0]; // Fallback to first org if no primary
}
```

### Organization Switcher

```javascript
async function switchOrganization(newOrgId) {
  // Verify user has access to this organization
  const response = await fetch('/orgarea/api/v1/organizations', {
    headers: { 'Authorization': `Bearer ${token}` }
  });
  
  const { data } = await response.json();
  const hasAccess = data.some(org => org.id === newOrgId);
  
  if (!hasAccess) {
    throw new Error('You do not have access to this organization');
  }
  
  // Switch to new organization
  localStorage.setItem('selected_org_id', newOrgId);
  
  // Reload organization-specific data
  await loadOrganizationData(newOrgId);
}
```

### Get Organization with Settings

```javascript
async function getOrganizationWithSettings(orgId) {
  const response = await fetch(`/orgarea/api/v1/organization/${orgId}?extend=settings`, {
    headers: { 'Authorization': `Bearer ${token}` }
  });
  
  const { data, included } = await response.json();
  
  // Parse settings
  const settings = {};
  if (included) {
    included.forEach(item => {
      if (item.type === 'customer_setting') {
        settings[item.attributes.type] = item.attributes.val_bool;
      }
    });
  }
  
  return {
    organization: data.attributes,
    settings: settings
  };
}
```

### Build Organization Selector UI

```javascript
async function buildOrganizationSelector() {
  const response = await fetch('/orgarea/api/v1/organizations', {
    headers: { 'Authorization': `Bearer ${token}` }
  });
  
  const { data } = await response.json();
  
  const selector = document.getElementById('org-selector');
  
  data.forEach(org => {
    const option = document.createElement('option');
    option.value = org.id;
    option.textContent = org.attributes.name;
    
    if (org.attributes.primary) {
      option.textContent += ' ⭐'; // Mark primary
      option.selected = true;
    }
    
    selector.appendChild(option);
  });
  
  selector.addEventListener('change', (e) => {
    switchOrganization(e.target.value);
  });
}
```

***

## Multi-Organization Workflow

### Complete Authentication + Organization Selection

```javascript
async function authenticateAndSelectOrg(email, password) {
  // Step 1: Login
  const loginResponse = await fetch('/orgarea/api/v1/login', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password, phone: '' })
  });
  
  const loginData = await loginResponse.json();
  const jwt_token = loginData.data.attributes.jwt_token;
  
  // Step 2: Get organizations
  const orgsResponse = await fetch('/orgarea/api/v1/organizations', {
    headers: { 'Authorization': `Bearer ${jwt_token}` }
  });
  
  const orgsData = await orgsResponse.json();
  
  // Step 3: Select organization
  let selectedOrgId;
  
  if (orgsData.data.length === 1) {
    // Auto-select single organization
    selectedOrgId = orgsData.data[0].id;
  } else {
    // Let user choose
    selectedOrgId = await promptUserToSelectOrganization(orgsData.data);
  }
  
  // Step 4: Store credentials
  return {
    jwt_token: jwt_token,
    organization_id: selectedOrgId,
    organization_name: orgsData.data.find(o => o.id === selectedOrgId).attributes.name
  };
}
```

***

## Timezones

Organizations can be configured with different timezones. This affects:

* Campaign start/end times
* Donation timestamps
* Report generation
* Scheduled notifications

**Common Timezones:**

* `America/New_York` - Eastern Time (US)
* `America/Los_Angeles` - Pacific Time (US)
* `America/Chicago` - Central Time (US)
* `Europe/London` - GMT/BST
* `Europe/Paris` - Central European Time
* `Asia/Jerusalem` - Israel Time
* `Australia/Sydney` - Australian Eastern Time

> **Tip:** Always display times in the organization's timezone when showing data to users.

***

## Languages

Organizations can set a default language that affects:

* Email notifications
* Receipt templates
* Dashboard interface (if applicable)
* Campaign pages

**Supported Languages:** See [Reference Data - Languages](reference.md#1-languages) for the complete list of 28 supported languages.

***

## Best Practices

## 1. Cache Organization Data

```javascript
const ORG_CACHE_DURATION = 60 * 60 * 1000; // 1 hour

async function getCachedOrganizations() {
  const cached = sessionStorage.getItem('organizations');
  const cacheTime = sessionStorage.getItem('organizations_cache_time');
  
  if (cached && cacheTime && Date.now() - cacheTime < ORG_CACHE_DURATION) {
    return JSON.parse(cached);
  }
  
  const response = await fetch('/orgarea/api/v1/organizations', {
    headers: { 'Authorization': `Bearer ${token}` }
  });
  
  const data = await response.json();
  sessionStorage.setItem('organizations', JSON.stringify(data));
  sessionStorage.setItem('organizations_cache_time', Date.now());
  
  return data;
}
```

## 2. Validate Organization Access

Always verify the user has access to an organization before making API calls:

```javascript
async function validateOrgAccess(orgId) {
  const orgs = await getCachedOrganizations();
  return orgs.data.some(org => org.id === orgId);
}
```

## 3. Handle Organization Switching

Clear organization-specific cached data when switching:

```javascript
function switchOrganization(newOrgId) {
  // Clear organization-specific cache
  sessionStorage.removeItem('campaigns');
  sessionStorage.removeItem('donations');
  sessionStorage.removeItem('org_settings');
  
  // Set new organization
  localStorage.setItem('selected_org_id', newOrgId);
  
  // Reload page or refresh data
  window.location.reload();
}
```

***

## Related Documentation

* [Authentication](authentication.md) - Login and obtain JWT token
* [Users & Contacts](../users-and-legal/users.md) - Manage organization users
* [Organization Settings](../organization/org-settings.md) - Configure organization-wide settings
* [Legal Entities](../users-and-legal/legal-entities.md) - Manage legal entities for receipts
