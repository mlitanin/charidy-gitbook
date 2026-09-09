# Settings

Configure organization-wide settings that affect all campaigns, users, and features.

## Overview

Organization settings control global configurations including:

* Available payment gateways
* Receipt email configurations
* Compliance requirements
* Feature flags and modules
* Soft delete permissions

***

## Endpoints

| Method | Endpoint & Description                                                                                                      |
| ------ | --------------------------------------------------------------------------------------------------------------------------- |
| `GET`  | <p><code>/organization/{orgId}/org_setting</code><br><strong>Get Settings.</strong> Retrieve all organization settings.</p> |

***

## Get Organization Settings

Retrieve all organization-wide settings and configurations.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/org_setting`

**Response:**

```json
[
  {
    "ID": 16052,
    "created_at": "2025-12-08T16:22:02-05:00",
    "updated_at": "2025-12-08T16:22:02-05:00",
    "deleted_at": null,
    "organization_id": 19763,
    "setting": "show_reports",
    "value": true,
    "value_int": 0,
    "value_extend": "[]"
  },
  {
    "ID": 0,
    "created_at": "0001-01-01T00:00:00Z",
    "updated_at": "0001-01-01T00:00:00Z",
    "deleted_at": null,
    "organization_id": 0,
    "setting": "dashboard_gateways_tab_list",
    "value": true,
    "value_int": 1,
    "value_extend": "{\"international_cc\": [...], \"israel_cc\": [...], \"vouchers\": [...], \"crypto\": [...], \"others\": [...]}"
  },
  {
    "ID": 0,
    "setting": "campaign_wizard_version",
    "value": true,
    "value_int": 1,
    "value_extend": "[{\"key\":\"chooseTemplate\",\"disabled\":false,\"defaults\":\"unidy-v2\"}]"
  },
  {
    "ID": 0,
    "setting": "receipt_email_gateway_list",
    "value": true,
    "value_int": 1,
    "value_extend": "[\"stripe\", \"paypal\", \"authorize\", \"check\", ...]"
  },
  {
    "ID": 0,
    "setting": "allow_soft_delete_any_gateway_donation",
    "value": true,
    "value_int": 0,
    "value_extend": "{\"for_gateways\": [\"stripe\", \"paypal\", \"check\", ...]}"
  },
  {
    "ID": 0,
    "setting": "account_legal_entities_compliance_questions",
    "value": true,
    "value_int": 0,
    "value_extend": "{\"us\": [\"Your organization has not had its federal tax-exempt status revoked...\"]}"
  }
]
```

### Setting Object

| Field             | Type    | Description                                       |
| ----------------- | ------- | ------------------------------------------------- |
| `ID`              | Integer | Setting ID (0 for default/system settings).       |
| `setting`         | String  | Setting name/key.                                 |
| `value`           | Boolean | Boolean value (if applicable).                    |
| `value_int`       | Integer | Integer value (if applicable).                    |
| `value_extend`    | String  | JSON string with extended configuration.          |
| `organization_id` | Integer | Organization ID (0 for system defaults).          |
| `created_at`      | String  | ISO 8601 timestamp when created.                  |
| `updated_at`      | String  | ISO 8601 timestamp when last updated.             |
| `deleted_at`      | String  | ISO 8601 timestamp when deleted (null if active). |

***

## Available Settings

## 1. show\_reports

Enable or disable the reports module for the organization.

**Type:** Boolean\
**Default:** `true`

**Example:**

```json
{
  "ID": 16052,
  "setting": "show_reports",
  "value": true,
  "value_int": 0,
  "value_extend": "[]"
}
```

***

## 2. dashboard\_gateways\_tab\_list

Configure which payment gateways are available to the organization.

**Type:** JSON Object in `value_extend`

**Structure:**

```json
{
  "international_cc": [
    {"key": "stripe", "label": "Stripe", "preferred": true},
    {"key": "paypal", "label": "PayPal"},
    {"key": "authorize", "label": "Authorize.Net"},
    {"key": "square", "label": "Square"},
    {"key": "braintree", "label": "Braintree"}
  ],
  "israel_cc": [
    {"key": "yaadpay", "label": "Yaad Pay"},
    {"key": "meshulam", "label": "Meshulam"},
    {"key": "cardcom", "label": "CardCom"},
    {"key": "pelecard", "label": "Pelecard"},
    {"key": "tranzila", "label": "Tranzila"}
  ],
  "vouchers": [
    {"key": "chariot", "label": "DAFpay by Chariot", "preferred": true},
    {"key": "peach", "label": "Peach"}
  ],
  "crypto": [
    {"key": "coinbase", "label": "Coinbase"}
  ],
  "others": [
    {"key": "check", "label": "Check"},
    {"key": "pledge", "label": "Pledge"}
  ]
}
```

**Categories:**

* `international_cc` - International credit card processors
* `israel_cc` - Israeli payment methods
* `vouchers` - DAF and voucher systems
* `crypto` - Cryptocurrency processors
* `others` - Other payment methods

***

## 3. campaign\_wizard\_version

Configure the campaign creation wizard behavior and defaults.

**Type:** JSON Array in `value_extend`

**Structure:**

```json
[
  {
    "key": "chooseTemplate",
    "disabled": false,
    "defaults": "unidy-v2"
  }
]
```

**Fields:**

* `key` - Wizard step identifier
* `disabled` - Whether this step is disabled
* `defaults` - Default value for this step

**Available Templates:**

* `unidy-v2` - Unidy V2 (modern template)
* `standard-v2` - Standard V2
* `classic` - Classic template

***

## 4. receipt\_email\_gateway\_list

List of payment gateways that automatically send receipt emails.

**Type:** JSON Array in `value_extend`

**Example:**

```json
[
  "stripe",
  "stripe-ach",
  "stripe-external",
  "stripe-apple-pay",
  "stripe-google-pay",
  "stripe-element",
  "paypal",
  "paypalv2",
  "payfast",
  "mercadopago",
  "payme",
  "payme-iframe",
  "checkout-fi",
  "paygate",
  "payrix",
  "dlocal",
  "ojc",
  "cardknox",
  "cardknox-google-apple-pay",
  "banquest",
  "banquest-ach",
  "usaepay",
  "authorize",
  "check",
  "square"
]
```

> **Tip:** If a gateway is in this list, Charidy will NOT send a duplicate receipt email - the gateway handles it.

***

## 5. allow\_soft\_delete\_any\_gateway\_donation

Configure which gateways allow soft deletion of donations.

**Type:** JSON Object in `value_extend`

**Structure:**

```json
{
  "for_gateways": [
    "achisomoch",
    "aminut",
    "asaas",
    "asserbishvil",
    "authorize",
    "bancontact",
    "banquest",
    "cardcom",
    "cardcom-bit",
    "cardknox",
    "cardknox-google-apple-pay",
    "chariot",
    "check",
    "checkout-fi",
    "cmz",
    "coinbase",
    "dlocal",
    "donary",
    "donorsfund",
    "icredit-rivhit",
    "israeltoremet",
    "jaffa",
    "jaffa-bit",
    "kolyom",
    "mercadopago",
    "meshulam",
    "meshulam-bank-transfer",
    "meshulam-bit",
    "meshulam-google-pay",
    "meshulam-v2",
    "meshulam-v2-bit",
    "nedarim-plus",
    "nedarim-plus-native",
    "nedarim-plus-native-bit",
    "ojc",
    "payarc",
    "payfast",
    "paygate",
    "paypal",
    "paypalv2",
    "peach",
    "peach-bit",
    "pelecard",
    "pledge",
    "pledger",
    "pledger-direct",
    "stripe",
    "sumit",
    "tevini",
    "tranzila",
    "tranzila-bit",
    "usaepay",
    "walletdoc",
    "walletdoc-direct",
    "yaadpay"
  ]
}
```

> **Warning:** Soft delete allows marking donations as deleted without permanently removing them from the database. This is useful for compliance and audit purposes.

***

## 6. account\_legal\_entities\_compliance\_questions

Compliance questions required for legal entities by country.

**Type:** JSON Object in `value_extend`

**Structure:**

```json
{
  "us": [
    "Your organization has not had its federal tax-exempt status revoked by the Internal Revenue Service",
    "Your organization has not had its tax-exempt status in California revoked by the state's Franchise Tax Board",
    "Your organization has not been prohibited from soliciting or operating in California by the state attorney general"
  ]
}
```

These questions must be answered when creating a US-based legal entity.

***

## Use Cases

### Get Specific Setting

```javascript
const response = await fetch('/orgarea/api/v1/organization/10001/org_setting', {
  headers: { 'Authorization': `Bearer ${token}` }
});

const settings = await response.json();
const showReports = settings.find(s => s.setting === 'show_reports');

if (showReports && showReports.value) {
  console.log('Reports module is enabled');
}
```

### Parse Gateway Configuration

```javascript
const settings = await getSettings();
const gatewaySetting = settings.find(s => s.setting === 'dashboard_gateways_tab_list');

if (gatewaySetting && gatewaySetting.value_extend) {
  const gateways = JSON.parse(gatewaySetting.value_extend);
  
  // Get all international gateways
  const internationalGateways = gateways.international_cc;
  
  // Get preferred gateways
  const preferred = internationalGateways.filter(g => g.preferred);
  
  console.log('Preferred gateways:', preferred.map(g => g.label));
}
```

### Check if Gateway Sends Receipts

```javascript
function gatewayHasAutoReceipt(gatewayKey, settings) {
  const receiptSetting = settings.find(s => s.setting === 'receipt_email_gateway_list');
  
  if (!receiptSetting || !receiptSetting.value_extend) {
    return false;
  }
  
  const gateways = JSON.parse(receiptSetting.value_extend);
  return gateways.includes(gatewayKey);
}

// Usage
if (gatewayHasAutoReceipt('stripe', settings)) {
  console.log('Stripe will automatically send receipt emails');
}
```

### Check if Gateway Allows Soft Delete

```javascript
function canSoftDelete(gatewayKey, settings) {
  const deleteSetting = settings.find(s => s.setting === 'allow_soft_delete_any_gateway_donation');
  
  if (!deleteSetting || !deleteSetting.value_extend) {
    return false;
  }
  
  const config = JSON.parse(deleteSetting.value_extend);
  return config.for_gateways.includes(gatewayKey);
}

// Usage
if (canSoftDelete('stripe', settings)) {
  console.log('Stripe donations can be soft deleted');
}
```

### Get Campaign Wizard Defaults

```javascript
const settings = await getSettings();
const wizardSetting = settings.find(s => s.setting === 'campaign_wizard_version');

if (wizardSetting && wizardSetting.value_extend) {
  const wizardConfig = JSON.parse(wizardSetting.value_extend);
  const templateStep = wizardConfig.find(step => step.key === 'chooseTemplate');
  
  console.log(`Default template: ${templateStep.defaults}`);
  console.log(`Template selection disabled: ${templateStep.disabled}`);
}
```

### Get Compliance Questions

```javascript
function getComplianceQuestions(country, settings) {
  const complianceSetting = settings.find(s => s.setting === 'account_legal_entities_compliance_questions');
  
  if (!complianceSetting || !complianceSetting.value_extend) {
    return [];
  }
  
  const compliance = JSON.parse(complianceSetting.value_extend);
  return compliance[country.toLowerCase()] || [];
}

// Usage
const usQuestions = getComplianceQuestions('US', settings);
usQuestions.forEach((question, index) => {
  console.log(`${index + 1}. ${question}`);
});
```

***

## Best Practices

## 1. Cache Settings

Settings rarely change, so cache them to reduce API calls:

```javascript
const CACHE_DURATION = 60 * 60 * 1000; // 1 hour

async function getCachedSettings() {
  const cached = sessionStorage.getItem('org_settings');
  const cacheTime = sessionStorage.getItem('org_settings_time');
  
  if (cached && cacheTime && Date.now() - cacheTime < CACHE_DURATION) {
    return JSON.parse(cached);
  }
  
  const response = await fetch('/orgarea/api/v1/organization/10001/org_setting', {
    headers: { 'Authorization': `Bearer ${token}` }
  });
  
  const settings = await response.json();
  sessionStorage.setItem('org_settings', JSON.stringify(settings));
  sessionStorage.setItem('org_settings_time', Date.now());
  
  return settings;
}
```

## 2. Parse JSON Safely

Always handle JSON parsing errors:

```javascript
function parseSettingValue(setting) {
  if (!setting.value_extend || setting.value_extend === '[]') {
    return null;
  }
  
  try {
    return JSON.parse(setting.value_extend);
  } catch (e) {
    console.error(`Failed to parse setting ${setting.setting}:`, e);
    return null;
  }
}
```

## 3. Provide Fallbacks

Always have fallback values:

```javascript
function getSetting(settings, key, defaultValue = null) {
  const setting = settings.find(s => s.setting === key);
  
  if (!setting) {
    return defaultValue;
  }
  
  if (setting.value_extend && setting.value_extend !== '[]') {
    return parseSettingValue(setting) || defaultValue;
  }
  
  return setting.value ?? defaultValue;
}

// Usage
const showReports = getSetting(settings, 'show_reports', true);
```

## 4. Distinguish System vs Organization Settings

Settings with `ID: 0` are system defaults. Settings with a real ID are organization-specific overrides.

```javascript
function isSystemDefault(setting) {
  return setting.ID === 0;
}

function isOrganizationOverride(setting) {
  return setting.ID > 0;
}
```

***

## Related Documentation

* [Payment Gateways](../payment-gateways/gateways.md) - Configure available gateways
* [Legal Entities](../users-and-legal/legal-entities.md) - Compliance questions for legal entities
* [Campaigns](../campaigns/campaigns.md) - Campaign wizard configuration
