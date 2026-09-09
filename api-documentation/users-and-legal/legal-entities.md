# Legal Entities

Manage your organization's legal entities used for issuing tax-deductible receipts and compliance documentation.

## Overview

Legal entities represent the official registered organizations that can issue tax receipts to donors. Organizations may have multiple legal entities for:

* Different countries or regions
* Multiple registered charities under one umbrella
* Separate 501(c)(3) organizations
* Different tax jurisdictions

> **Tip:** Primary Entity Only one entity can be marked as `primary` per organization. The primary entity is used as the default for new campaigns and appears first in the dashboard list.

**Dashboard Display Format:** `[ID - Country] Name`\
Example: `[17183 - IL] בית אלנעמה (ע"ר")`

> **Info:** Request Charidy Foundation Organizations can request to use the Charidy Foundation as a legal entity for issuing receipts. This option appears in the dashboard as "Request Charidy Foundation" button.

***

## Endpoints

| Method | Endpoint & Description                                                                                                        |
| ------ | ----------------------------------------------------------------------------------------------------------------------------- |
| `GET`  | <p><code>/organization/{orgId}/account/entities</code><br><strong>List Entities.</strong> Get all legal entities.</p>         |
| `POST` | <p><code>/organization/{orgId}/legal-entities</code><br><strong>Create Entity.</strong> Add a new legal entity.</p>           |
| `PUT`  | <p><code>/organization/{orgId}/legal-entities/{entityId}</code><br><strong>Update Entity.</strong> Modify entity details.</p> |

***

## 1. Get Legal Entities

Retrieve all legal entities registered to the organization.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/account/entities`

**Response:**

```json
{
  "data": [
    {
      "type": "org_legal_entity",
      "id": "17183",
      "attributes": {
        "type": "nonprofit",
        "name": "Example Charity Inc.",
        "tax_id": "12-3456789",
        "address_line_1": "123 Main Street",
        "address_line_2": "Suite 100",
        "city": "New York",
        "state": "New York",
        "zip": "10001",
        "country": "US",
        "tax_deductible_receipt": true,
        "receipt_logo": "https://cdn.charidy.com/receipts/logo.png",
        "primary": true,
        "created_at": 1766569081,
        "lat": 40.7128,
        "lng": -74.0060
      }
    }
  ]
}
```

### Entity Attributes

| Attribute                | Type    | Description                                               |
| ------------------------ | ------- | --------------------------------------------------------- |
| `id`                     | String  | Entity ID.                                                |
| `type`                   | String  | Entity type: `nonprofit` or `regular`.                    |
| `name`                   | String  | Legal entity name (as registered).                        |
| `tax_id`                 | String  | Tax identification number (required if type=nonprofit).   |
| `address_line_1`         | String  | Street address.                                           |
| `address_line_2`         | String  | Additional address line (suite, building, etc.).          |
| `city`                   | String  | City.                                                     |
| `state`                  | String  | State/Province (required for US/CA).                      |
| `zip`                    | String  | Postal/ZIP code.                                          |
| `country`                | String  | 2-letter ISO country code (e.g., `US`, `IL`, `CA`, `GB`). |
| `tax_deductible_receipt` | Boolean | Whether donations are tax-deductible.                     |
| `receipt_logo`           | String  | Logo URL to display on receipts.                          |
| `primary`                | Boolean | Whether this is the primary/default entity.               |
| `created_at`             | Integer | Unix timestamp when entity was created.                   |
| `lat`                    | Number  | Latitude (for mapping).                                   |
| `lng`                    | Number  | Longitude (for mapping).                                  |

***

## 2. Create Legal Entity

Add a new legal entity to the organization.

**Endpoint:** `POST /api/organizations/{orgId}/legal-entities`

**Headers:**

```http
Content-Type: application/json
```

**Request Body:**

```json
{
  "data": {
    "type": "org_legal_entity",
    "attributes": {
      "type": "nonprofit",
      "name": "Example Charity Inc.",
      "tax_id": "12-3456789",
      "address_line_1": "123 Main St",
      "address_line_2": "Suite 100",
      "city": "New York",
      "state": "New York",
      "zip": "10001",
      "country": "US",
      "tax_deductible_receipt": true,
      "receipt_logo": "https://cdn.example.com/logo.png",
      "primary": false
    }
  },
  "included": []
}
```

### Required Fields

| Field                    | Type    | Description                                          |
| ------------------------ | ------- | ---------------------------------------------------- |
| `type`                   | String  | **Required.** Entity type: `nonprofit` or `regular`. |
| `name`                   | String  | **Required.** Legal entity name.                     |
| `tax_id`                 | String  | **Required if type=nonprofit.** Tax ID number.       |
| `address_line_1`         | String  | **Required.** Street address.                        |
| `city`                   | String  | **Required.** City.                                  |
| `state`                  | String  | **Required for US/CA.** State/Province.              |
| `zip`                    | String  | **Required.** Postal/ZIP code.                       |
| `country`                | String  | **Required.** 2-letter ISO country code.             |
| `tax_deductible_receipt` | Boolean | **Required.** Tax-deductible status.                 |

## Optional Fields

| Field               | Type    | Description                                     |
| ------------------- | ------- | ----------------------------------------------- |
| `address_line_2`    | String  | Additional address line.                        |
| `receipt_logo`      | String  | Logo URL (upload via `/upload-image` endpoint). |
| `primary`           | Boolean | Set as primary entity (default: false).         |
| `receipt_config_fr` | Object  | France-specific receipt configuration.          |
| `receipt_config_ca` | Object  | Canada-specific receipt configuration.          |
| `receipt_config_ge` | Object  | Germany-specific receipt configuration.         |
| `receipt_config_nz` | Object  | New Zealand-specific receipt configuration.     |
| `receipt_config_il` | Object  | Israel-specific receipt configuration.          |
| `receipt_config_za` | Object  | South Africa-specific receipt configuration.    |

> **Warning:** Important Only include the receipt config for the selected country. For example, if `country` is `US`, you don't need any country-specific configs.

***

## 3. Update Legal Entity

Modify an existing legal entity's details.

**Endpoint:** `PUT /api/organizations/{orgId}/legal-entities/{entityId}`

**Headers:**

```http
Content-Type: application/json
```

**Request Body:**

```json
{
  "data": {
    "type": "org_legal_entity",
    "id": "17183",
    "attributes": {
      "address_line_1": "456 New Address",
      "city": "Boston",
      "state": "Massachusetts",
      "zip": "02101",
      "receipt_logo": "https://cdn.example.com/new-logo.png"
    }
  },
  "included": []
}
```

> **Tip:** You only need to include the fields you want to update. All other fields will remain unchanged.

***

## Country-Specific Receipt Configurations

### 🇫🇷 France (`receipt_config_fr`)

For French legal entities, include detailed tax compliance information.

```json
{
  "receipt_config_fr": {
    "is_200_du_cgi": true,
    "is_238_bis_du_cgi": false,
    "is_885_0v_bis_du_cgi": false,
    "is_978_du_cgi_impot_sur_la_fortune_immobiliere": false,
    "acte_sous_seing_prive": true,
    "declaration_de_don_manuel": false,
    "autres1": false,
    "acte_authentique": false,
    "titres_de_societes_cotes": false,
    "numeraire": true,
    "autres2": false,
    "remise_d_especes": false,
    "cheque": true,
    "bsd": false,
    "is_oeuvre": true,
    "object": "Soutien aux œuvres caritatives",
    "receipt_prefix": "FR",
    "signature": "https://cdn.example.com/signature.png"
  }
}
```

**Fields:**

| Field                                            | Type    | Description                                   |
| ------------------------------------------------ | ------- | --------------------------------------------- |
| `is_200_du_cgi`                                  | Boolean | Article 200 du CGI (general donations).       |
| `is_238_bis_du_cgi`                              | Boolean | Article 238 bis du CGI (corporate donations). |
| `is_885_0v_bis_du_cgi`                           | Boolean | Article 885-0 V bis du CGI (wealth tax).      |
| `is_978_du_cgi_impot_sur_la_fortune_immobiliere` | Boolean | Article 978 du CGI (real estate wealth tax).  |
| `acte_sous_seing_prive`                          | Boolean | Private deed.                                 |
| `declaration_de_don_manuel`                      | Boolean | Manual donation declaration.                  |
| `autres1`                                        | Boolean | Other (category 1).                           |
| `acte_authentique`                               | Boolean | Authentic deed.                               |
| `titres_de_societes_cotes`                       | Boolean | Listed company shares.                        |
| `numeraire`                                      | Boolean | Cash donation.                                |
| `autres2`                                        | Boolean | Other (category 2).                           |
| `remise_d_especes`                               | Boolean | Cash handover.                                |
| `cheque`                                         | Boolean | Check payment.                                |
| `bsd`                                            | Boolean | BSD (specific French tax form).               |
| `is_oeuvre`                                      | Boolean | Charitable work status.                       |
| `object`                                         | String  | Purpose/object of the organization.           |
| `receipt_prefix`                                 | String  | Receipt number prefix.                        |
| `signature`                                      | String  | Signature image URL.                          |

***

### 🇨🇦 Canada (`receipt_config_ca`)

For Canadian charities registered with CRA.

```json
{
  "receipt_config_ca": {
    "bh": true,
    "signature": "https://cdn.example.com/signature.png",
    "message": "<p>Thank you for your donation!</p>",
    "full_name": "John Smith",
    "tax_phone_number": "+1-555-123-4567"
  }
}
```

**Fields:**

| Field              | Type    | Description                             |
| ------------------ | ------- | --------------------------------------- |
| `bh`               | Boolean | BH (specific Canadian tax designation). |
| `signature`        | String  | Signature image URL.                    |
| `message`          | String  | Custom HTML message for receipts.       |
| `full_name`        | String  | Full name of authorized signatory.      |
| `tax_phone_number` | String  | Phone number for tax inquiries.         |

***

### 🇩🇪 Germany (`receipt_config_ge`)

For German charitable organizations (gemeinnützig).

```json
{
  "receipt_config_ge": {
    "signature": "https://cdn.example.com/signature.png",
    "purpose": "Förderung von Bildung und Wissenschaft",
    "tax_number": "12/345/67890",
    "bank": "Deutsche Bank",
    "iban": "DE89370400440532013000",
    "bic": "DEUTDEDBBER",
    "assessment_period": "2023-2025",
    "term_1": true,
    "term_2": true,
    "term_3": false,
    "term_4": true
  }
}
```

**Fields:**

| Field               | Type    | Description                         |
| ------------------- | ------- | ----------------------------------- |
| `signature`         | String  | Signature image URL.                |
| `purpose`           | String  | Charitable purpose (Satzungszweck). |
| `tax_number`        | String  | Tax number (Steuernummer).          |
| `bank`              | String  | Bank name.                          |
| `iban`              | String  | IBAN for bank transfers.            |
| `bic`               | String  | BIC/SWIFT code.                     |
| `assessment_period` | String  | Tax assessment period.              |
| `term_1`            | Boolean | Compliance term 1.                  |
| `term_2`            | Boolean | Compliance term 2.                  |
| `term_3`            | Boolean | Compliance term 3.                  |
| `term_4`            | Boolean | Compliance term 4.                  |

***

### 🇳🇿 New Zealand (`receipt_config_nz`)

For New Zealand charities registered with Charities Services.

```json
{
  "receipt_config_nz": {
    "nzbn": "9429000000000",
    "name_position": "Executive Director",
    "signature": "https://cdn.example.com/signature.png",
    "ird": "123-456-789"
  }
}
```

**Fields:**

| Field           | Type   | Description                             |
| --------------- | ------ | --------------------------------------- |
| `nzbn`          | String | New Zealand Business Number.            |
| `name_position` | String | Position/title of signatory.            |
| `signature`     | String | Signature image URL.                    |
| `ird`           | String | IRD (Inland Revenue Department) number. |

***

### 🇮🇱 Israel (`receipt_config_il`)

For Israeli organizations with Section 46 tax-exempt status.

```json
{
  "receipt_config_il": {
    "section_46": true
  }
}
```

**Fields:**

| Field        | Type    | Description                                            |
| ------------ | ------- | ------------------------------------------------------ |
| `section_46` | Boolean | Whether organization has Section 46 tax-exempt status. |

**Receipt Text (Hebrew):**

> "תרומתך מוכרת לצורכי מס לפי סעיף 46 לפקודת מס הכנסה."

***

### 🇿🇦 South Africa (`receipt_config_za`)

For South African Section 18A organizations.

```json
{
  "receipt_config_za": {
    "signature": "https://cdn.example.com/signature.png"
  }
}
```

**Fields:**

| Field       | Type   | Description          |
| ----------- | ------ | -------------------- |
| `signature` | String | Signature image URL. |

***

## Upload Receipt Logo

Before creating or updating a legal entity with a logo, you must first upload the image.

**Endpoint:** `POST /api/organizations/{orgId}/upload-image`

**Headers:**

```http
Content-Type: multipart/form-data
```

**Request:**

```http
POST /api/organizations/19763/upload-image
Content-Type: multipart/form-data

------WebKitFormBoundary
Content-Disposition: form-data; name="file"; filename="logo.png"
Content-Type: image/png

[binary image data]
------WebKitFormBoundary--
```

**Response:**

```json
{
  "url": "https://cdn.charidy.com/uploads/logo-12345.png"
}
```

Use the returned URL in the `receipt_logo` field.

***

## Country Codes

Use 2-letter ISO country codes:

| Code | Country        | Code | Country      |
| ---- | -------------- | ---- | ------------ |
| `US` | United States  | `CA` | Canada       |
| `GB` | United Kingdom | `IL` | Israel       |
| `FR` | France         | `DE` | Germany      |
| `NZ` | New Zealand    | `ZA` | South Africa |
| `AU` | Australia      | `NL` | Netherlands  |
| `BE` | Belgium        | `CH` | Switzerland  |
| `AT` | Austria        | `ES` | Spain        |
| `IT` | Italy          | `PT` | Portugal     |

For a complete list, use the [Countries API](../getting-started/reference.md#2-countries).

***

## State Validation

For **US** and **CA** entities, the `state` field is **required** and must match a valid state/province.

### United States States

Alabama, Alaska, Arizona, Arkansas, California, Colorado, Connecticut, Delaware, Florida, Georgia, Hawaii, Idaho, Illinois, Indiana, Iowa, Kansas, Kentucky, Louisiana, Maine, Maryland, Massachusetts, Michigan, Minnesota, Mississippi, Missouri, Montana, Nebraska, Nevada, New Hampshire, New Jersey, New Mexico, New York, North Carolina, North Dakota, Ohio, Oklahoma, Oregon, Pennsylvania, Rhode Island, South Carolina, South Dakota, Tennessee, Texas, Utah, Vermont, Virginia, Washington, West Virginia, Wisconsin, Wyoming

### Canadian Provinces

Alberta, British Columbia, Manitoba, New Brunswick, Newfoundland and Labrador, Northwest Territories, Nova Scotia, Nunavut, Ontario, Prince Edward Island, Quebec, Saskatchewan, Yukon

***

## Use Cases

### Create US Nonprofit

```javascript
const response = await fetch('/api/organizations/19763/legal-entities', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    data: {
      type: 'org_legal_entity',
      attributes: {
        type: 'nonprofit',
        name: 'My Charity Foundation',
        tax_id: '12-3456789',
        address_line_1: '100 Charity Lane',
        city: 'New York',
        state: 'New York',
        zip: '10001',
        country: 'US',
        tax_deductible_receipt: true,
        primary: true
      }
    },
    included: []
  })
});
```

### Create Canadian Charity

```javascript
const response = await fetch('/api/organizations/19763/legal-entities', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    data: {
      type: 'org_legal_entity',
      attributes: {
        type: 'nonprofit',
        name: 'Canadian Charity Inc.',
        tax_id: '123456789RR0001',
        address_line_1: '456 Maple Avenue',
        city: 'Toronto',
        state: 'Ontario',
        zip: 'M5H 2N2',
        country: 'CA',
        tax_deductible_receipt: true,
        receipt_config_ca: {
          bh: true,
          signature: 'https://cdn.example.com/signature.png',
          full_name: 'Jane Doe',
          tax_phone_number: '+1-416-555-1234'
        }
      }
    },
    included: []
  })
});
```

### Create Israeli Amuta

```javascript
const response = await fetch('/api/organizations/19763/legal-entities', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    data: {
      type: 'org_legal_entity',
      attributes: {
        type: 'nonprofit',
        name: 'עמותת צדקה',
        tax_id: '580123456',
        address_line_1: 'רחוב הרצל 123',
        city: 'ירושלים',
        zip: '9458123',
        country: 'IL',
        tax_deductible_receipt: true,
        receipt_config_il: {
          section_46: true
        }
      }
    },
    included: []
  })
});
```

### Update Entity Address

```javascript
const response = await fetch('/api/organizations/19763/legal-entities/17183', {
  method: 'PUT',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    data: {
      type: 'org_legal_entity',
      id: '17183',
      attributes: {
        address_line_1: '789 New Street',
        city: 'Boston',
        state: 'Massachusetts',
        zip: '02101'
      }
    },
    included: []
  })
});
```

### Upload and Set Logo

```javascript
// Step 1: Upload image
const formData = new FormData();
formData.append('file', logoFile);

const uploadResponse = await fetch('/api/organizations/19763/upload-image', {
  method: 'POST',
  headers: { 'Authorization': `Bearer ${token}` },
  body: formData
});

const { url } = await uploadResponse.json();

// Step 2: Update entity with logo URL
await fetch('/api/organizations/19763/legal-entities/17183', {
  method: 'PUT',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    data: {
      type: 'org_legal_entity',
      id: '17183',
      attributes: {
        receipt_logo: url
      }
    },
    included: []
  })
});
```

***

## Best Practices

## 1. Verify Tax ID Format

Different countries have different tax ID formats:

| Country | Format          | Example         |
| ------- | --------------- | --------------- |
| US      | XX-XXXXXXX      | 12-3456789      |
| Canada  | XXXXXXXXXRRXXXX | 123456789RR0001 |
| Israel  | 9 digits        | 580123456       |
| UK      | 7 digits        | 1234567         |

## 2. One Primary Entity

Only one entity can be marked as `primary`. If you create a new entity with `primary: true`, the previous primary entity will automatically be set to `primary: false`.

## 3. Country-Specific Configs

Only include the receipt config that matches the entity's country:

* `country: "FR"` → include `receipt_config_fr`
* `country: "CA"` → include `receipt_config_ca`
* `country: "US"` → no config needed

## 4. Upload Images First

Always upload logos and signatures using the `/upload-image` endpoint before referencing them in the entity.

***

## Validation Rules

### Required Fields by Country

**All Countries:**

* `type`, `name`, `address_line_1`, `city`, `zip`, `country`, `tax_deductible_receipt`

**US & Canada:**

* `state` (must be valid state/province)

**Nonprofit Entities:**

* `tax_id` (required if `type` = "nonprofit")

### Image Formats

**Supported formats:**

* PNG, JPG, JPEG, GIF
* Maximum size: 5MB
* Recommended: 300x300px for logos

***

## Related Documentation

* [Reference Data](../getting-started/reference.md) - Countries and currencies
* [Gateways](../payment-gateways/gateways.md) - Link gateways to legal entities
* [Donations](../campaigns/donations.md) - Receipts are issued from legal entities
