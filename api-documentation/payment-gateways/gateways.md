# Overview

Configure payment gateways for your organization to accept donations through various payment processors.

## Overview

Gateways are payment processors that handle credit card, bank transfer, cryptocurrency, and other payment methods. Each organization can configure multiple gateways to support different:

* Payment methods (credit card, ACH, crypto, vouchers)
* Currencies (USD, EUR, ILS, etc.)
* Geographic regions (US, Israel, Europe, etc.)
* Legal entities (for tax receipts)

***

## Endpoints

| Method | Endpoint & Description                                                                                                                                 |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `GET`  | <p><code>/organization/{orgId}/gateways</code><br><strong>List Gateways.</strong> Get all available gateways for the organization.</p>                 |
| `GET`  | <p><code>/organization/{orgId}/gateway/{gateway}/add</code><br><strong>Get Gateway Config.</strong> Get configuration schema for adding a gateway.</p> |

***

## 1. Get Organization Gateways

Returns a list of gateways available for the specified organization.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/gateways`

**Response Structure:**

```json
{
  "data": [
    {
      "type": "gateway",
      "id": "123",
      "attributes": {
        "gateway": "stripe",
        "status": "active",
        "currency": "USD",
        "legal_entity_id": 17183
      }
    },
    {
      "type": "gateway",
      "id": "124",
      "attributes": {
        "gateway": "paypal",
        "status": "active",
        "currency": "USD",
        "legal_entity_id": 17183
      }
    }
  ]
}
```

**Gateway Codes:**

Each `data[i].attributes.gateway` represents a gateway code that can be used in the next endpoint.

***

## 2. Get Gateway Configuration

Returns the configuration schema for the selected gateway. Everything inside `data.attributes` represents a field that the user must configure in the "Add Gateway" form.

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/gateway/{gateway}/add`

**Parameters:**

* `{gateway}` - Gateway code from the previous endpoint (e.g., `stripe`, `paypal`, `cardcom`)

**Example Response:**

```json
{
  "data": {
    "type": "",
    "attributes": {
      "auto_create_account": false,
      "available_currency_list": [
        { "text": "Pound Sterling (GBP)", "value": "GBP" },
        { "text": "US Dollar (USD)", "value": "USD" },
        { "text": "Euro (EUR)", "value": "EUR" },
        { "text": "Australian Dollar (AUD)", "value": "AUD" },
        { "text": "Canadian Dollar (CAD)", "value": "CAD" },
        { "text": "Israeli Shekel (ILS)", "value": "ILS" },
        { "text": "New Zealand Dollar (NZD)", "value": "NZD" },
        { "text": "Swiss Franc (CHF)", "value": "CHF" },
        { "text": "Russian Ruble (RUB)", "value": "RUB" },
        { "text": "Danish krone (DKK)", "value": "DKK" },
        { "text": "Japanese Yen (JPY)", "value": "JPY" }
      ],
      "card_owner_info": false,
      "currency": "",
      "department_id": "",
      "invoice_lang": "",
      "invoice_type": "",
      "legal_entity_id": 0,
      "status": false,
      "terminal_number": "",
      "user_name": "",
      "user_password": ""
    }
  }
}
```

> **Tip:** Some fields may be required depending on the gateway. See the Gateway Reference below for specific requirements.

***

## Gateway Reference

### International Credit Card Processors

## 1. Stripe

**Gateway Code:** `stripe`

**Required Fields:**

* `type` - Must be "individual" or "company"
* `url` - URL must be defined

**Optional Fields:**

* Address fields, bank account details, metadata

**Supported Currencies:** Multiple (USD, EUR, GBP, CAD, AUD, etc.)

***

## 2. PayPal

**Gateway Code:** `paypal`

**Required Fields (Standard):**

* `email` - PayPal account email
* `api_key` - API key
* `api_secret` - API secret
* `currency` - Currency code

**Required Fields (V2):**

* `api_key` - API key
* `api_secret` - API secret
* `currency` - Currency code

**Optional Fields:**

* `legal_entity_id`, `status`, `test`, `available_currency_list`

**Supported Currencies:** Multiple

***

## 3. Authorize.Net

**Gateway Code:** `authorize`

**Required Fields:**

* `api_login_id` - API Login ID
* `transaction_key` - Transaction Key
* `client_key` - Client Key

**Optional Fields:**

* `legal_entity_id`, `available_currency_list`, `currency`, `status`

**Supported Currencies:** USD, CAD, EUR, GBP

***

## 4. Braintree

**Gateway Code:** `braintree`

**Required Fields:**

* `merch_id` - Merchant ID
* `pub_key` - Public Key
* `priv_key` - Private Key

**Optional Fields:**

* `currency`, `status`

**Supported Currencies:** Multiple

***

## 5. Square

**Gateway Code:** `square`

**Required Fields:**

* `currency` - Currency code
* `application_id` - Application ID
* `access_token` - Access Token
* `location` - Location ID

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** USD, CAD, AUD, GBP, JPY

***

### Israeli Payment Gateways

## 6. CardCom

**Gateway Code:** `cardcom`

**Required Fields:**

* `currency` - Currency code
* `user_name` - Username
* `user_password` - Password
* `terminal_number` - Terminal number

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`, `invoice_lang`, `invoice_type`, `department_id`, `auto_create_account`, `card_owner_info`

**Supported Currencies:** ILS, USD, EUR

***

## 7. Pelecard

**Gateway Code:** `pelecard`

**Required Fields:**

* `user` - Username
* `terminal` - Terminal ID
* `password` - Password

**Conditionally Required:**

* If `receipt_method` = "ezcount": `ezcount_api_key`, and either `ezcount_api_email` OR `ezcount_developer_email`
* If `receipt_method` = "icount": `icount_cid`, `icount_user`, `icount_pass`
* If `add_bit_button` = true: `gama_client_id`, `gama_client_secret`

**Optional Fields:**

* `legal_entity_id`, `currency`, `status`, `available_currency_list`, `receipt_method`, `receipt_doc_type`, `recurring_via_j5`, `ezcount_corporation_id`, `add_bit_button`

**Supported Currencies:** ILS, USD, EUR

***

## 8. Tranzila

**Gateway Code:** `tranzila`

**Required Fields:**

* `terminal_name` - Terminal name
* `tranzila_pw` - Tranzila password
* `private_key` - Private key
* `public_key` - Public key
* `currency` - Currency code

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`, `paypal`

**Supported Currencies:** ILS, USD, EUR

***

## 9. Meshulam

**Gateway Code:** `meshulam`

**Required Fields (when not new account):**

* `user_id` - User ID

**Optional Fields:**

* `legal_entity_id`, `api_key`, `currency`, `status`, `available_currency_list`, `org_custom_id`, `fallback_phone`, `new_user`, `new_user_phone`, `new_user_quote`, `new_user_business_number`

**Supported Currencies:** ILS

***

## 10. YaadPay

**Gateway Code:** `yaadpay`

**Required Fields:**

* `terminal_id` - Terminal ID
* `api_password` - API password
* `signature_key` - Signature key
* `currency` - Currency code

**Optional Fields:**

* `legal_entity_id`, `max_payments`, `installments`, `zpass`, `page_lang`, `status`, `emv`, `charidy_subaccount`, all available lists

**Supported Currencies:** ILS

***

## 11. iCredit Rivhit

**Gateway Code:** `icredit_rivhit`

**Required Fields:**

* `currency` - Currency code
* `group_private_token` - Group private token
* `invoice_lang` - Invoice language
* `exempt_vat` - VAT exemption status

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`, `recurring_group_private_token`, `catalog_number`

**Supported Currencies:** ILS

***

## 12. Nedarim Plus

**Gateway Code:** `nedarimplus`

**Required Fields:**

* `mosad_id` - Institution ID
* `api_valid` - API validation key
* `currency` - Currency code

**Optional Fields:**

* `legal_entity_id`, `status`, `api_password`, `required_id_number`, `available_currency_list`, `recurring_via_j5`

**Supported Currencies:** ILS

***

## 13. Jaffa

**Gateway Code:** `jaffa`

**Required Fields:**

* `currency` - Currency code
* `client_id` - Client ID

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`, `form_lang`

**Supported Currencies:** ILS

***

## 14. Jaffa Bit

**Gateway Code:** `jaffa_bit`

**Required Fields:**

* `currency` - Currency code
* `client_id` - Client ID

**Conditionally Required:**

* If `form_lang` is provided, it must be "he" (Hebrew only)

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`, `form_lang`

**Supported Currencies:** ILS

***

## 15. Tranzila Bit

**Gateway Code:** `tranzila_bit`

**Required Fields:**

* `terminal_name` - Terminal name
* `private_key` - Private key
* `public_key` - Public key
* `currency` - Currency code

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** ILS

***

## 16. Meshulam Bit

**Gateway Code:** `meshulam_bit`

**Required Fields:**

* `user_id` - User ID

**Optional Fields:**

* `legal_entity_id`, `api_key`, `currency`, `status`, `available_currency_list`

**Supported Currencies:** ILS

***

## 17. Gama

**Gateway Code:** `gama`

**Required Fields:**

* `clientId` - Client ID
* `clientSecret` - Client secret

**Optional Fields:**

* `legal_entity_id`, `currency`, `status`, `available_currency_list`

**Supported Currencies:** ILS

***

### US-Based Gateways

## 18. Cardknox

**Gateway Code:** `cardknox`

**Required Fields:**

* `currency` - Currency code
* `ifields_key` - iFields key
* `x_key` - X key
* `type` - Must be "cc" or "check"

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`, `disable_card_swiper`

**Supported Currencies:** USD

***

## 19. USAePay

**Gateway Code:** `usaepay`

**Required Fields:**

* `api_key` - API key
* `pin` - PIN
* `currency` - Currency code

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** USD

***

## 20. Banquest

**Gateway Code:** `banquest`

**Required Fields:**

* `api_source_key` - API source key
* `api_pin` - API PIN

**Conditionally Required:**

* If card=true: `tokenization_public_key` - Tokenization public key
* For ValidateTerminal(): `terminal_id` - Terminal ID

**Optional Fields:**

* `legal_entity_id`, `available_currency_list`, `currency`, `status`, `campaign_id`, `existing_banquest`

**Supported Currencies:** USD

***

## 21. Payrix

**Gateway Code:** `payrix`

**Required Fields:**

* `merchant_id` - Merchant ID
* `currency` - Must be "usd"

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** USD

***

### International Payment Gateways

## 22. Payarc

**Gateway Code:** `payarc`

**Required Fields:**

* `currency` - Currency code
* `client_id` - Client ID
* `secret_key` - Secret key

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** USD, CAD

***

## 23. ASAAS (Brazil)

**Gateway Code:** `asaas`

**Required Fields:**

* `currency` - Currency code
* `api_key` - API key

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`, `billing_type`

**Supported Currencies:** BRL

***

## 24. MercadoPago (Latin America)

**Gateway Code:** `mercadopago`

**Required Fields:**

* `currency` - Must be "mxn", "ars", or "clp"
* `public_key` - Public token
* `access_token` - Access token

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** MXN, ARS, CLP

***

## 25. DLocal (Latin America)

**Gateway Code:** `dlocal`

**Required Fields:**

* Valid `legal_entity_id` with supported country code
* Valid `currency` for the country

**Optional Fields:**

* `status`, `available_currency_list`

**Supported Currencies:** Multiple (country-dependent)

***

## 26. Checkout.fi (Finland)

**Gateway Code:** `checkoutfi`

**Required Fields:**

* `account_number` - Account number
* `secret_key` - Secret key
* `currency` - Must be "eur"

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** EUR

***

## 27. PayFast (South Africa)

**Gateway Code:** `payfast`

**Required Fields:**

* `merchant_id` - Merchant ID
* `merchant_key` - Merchant key
* `passphrase` - Passphrase
* `currency` - Currency code

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** ZAR

***

## 28. PayGate (South Africa)

**Gateway Code:** `paygate`

**Required Fields:**

* `paygate_id` - PayGate ID
* `password` - Password
* `currency` - Must be "zar", "eur", or "usd"

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** ZAR, EUR, USD

***

## 29. Razorpay (India)

**Gateway Code:** `razorpay`

**Required Fields:**

* `currency` - Currency code
* `key_id` - Key ID
* `key_secret` - Key secret

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** INR

***

### Cryptocurrency Gateways

## 30. Coinbase

**Gateway Code:** `coinbase`

**Required Fields:**

* `currency` - Currency code
* `webhook_secret_key` - Webhook secret key
* `api_key` - API key

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** BTC, ETH, USDC, etc.

***

### Alternative Payment Methods

## 31. Peach (DAF/Vouchers)

**Gateway Code:** `peach`

**Required Fields:**

* `url_id` - URL ID

**Optional Fields:**

* `legal_entity_id`, `currency`, `status`, `available_currency_list`

***

## 32. PayMe (Israel)

**Gateway Code:** `payme`

**Required Fields:**

* `seller_file_social_id` - Social ID file
* `seller_file_cheque` - Bank account ownership or cancelled cheque photo
* `seller_file_corporate` - Incorporation document photo

**Optional Fields:**

* All other seller fields, `bank_account_currency`, `legal_entity_id`, etc.

**Supported Currencies:** ILS

***

## 33. HK Bank Transfer (Israel)

**Gateway Code:** `hk_bank_transfer`

**Required Fields:**

* `currency` - Must be "ils"
* `inst_number` - Institution number
* `fax` - Fax number

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

**Supported Currencies:** ILS

***

## 34. Sumit

**Gateway Code:** `sumit`

**Required Fields:**

* `company_id` - Company ID
* `api_key` - API key
* `public_key` - Public key

**Optional Fields:**

* `legal_entity_id`, `currency`, `status`, `available_currency_list`

***

### Campaign-Based Gateways

These gateways are tied to specific campaigns and require campaign-specific credentials.

## 35. Achisomoch

**Gateway Code:** `achisomoch`

**Required Fields:**

* `campaign_id` - Campaign ID

**Optional Fields:**

* `legal_entity_id`, `currency`, `secret_key`, `status`

***

## 36. Kol Yom

**Gateway Code:** `kolyom`

**Required Fields:**

* `campaign_id` - Campaign ID
* `hash_string` - Hash string

**Optional Fields:**

* `legal_entity_id`, `currency`, `status`

***

## 37. Tevini

**Gateway Code:** `tevini`

**Required Fields:**

* `campaign_id` - Campaign ID
* `hash_string` - Hash string
* `identifier` - Identifier

**Optional Fields:**

* `legal_entity_id`, `currency`, `status`

***

## 38. Asser Bishvil

**Gateway Code:** `asserbishvil`

**Required Fields:**

* `campaign_id` - Campaign ID
* `hash_string` - Hash string

**Optional Fields:**

* `legal_entity_id`, `currency`, `status`

***

## 39. CMZ

**Gateway Code:** `cmz`

**Required Fields:**

* `campaign_id` - Campaign ID
* `hash_string` - Hash string

**Optional Fields:**

* `legal_entity_id`, `currency`, `status`

***

### Special Purpose Gateways

## 40. Aminut

**Gateway Code:** `aminut`

**Required Fields:**

* `currency` - Currency code
* `api_url` - API URL

**Optional Fields:**

* `legal_entity_id`, `status`, `available_currency_list`

***

## 41. OJC

**Gateway Code:** `ojc`

**Required for OJC Card:**

* `number` - OJC card number (16 digits)
* `month` - Card expiration month
* `year` - Card expiration year

**Required for OJC Struct:**

* `tax_id` - Tax ID
* `ojc_org_id` - OJC Organization ID

**Optional Fields:**

* `legal_entity_id`, `mid`, `available_organization_list`

***

## 42. Donary

**Gateway Code:** `donary`

**All Optional:**

* `legal_entity_id`, `currency`, `status`

***

## 43. Israel Toremet

**Gateway Code:** `israel_toremet`

**Required Fields:**

* `gov_id` - Government ID
* `project_id` - Project ID

**Optional Fields:**

* `legal_entity_id`, `max_payments`, `installments`, `currency`, `status`, all available lists

**Supported Currencies:** ILS

***

## Gateway Categories

### By Region

**United States:**

* Stripe, PayPal, Authorize.Net, Braintree, Square, Cardknox, USAePay, Banquest, Payrix

**Israel:**

* CardCom, Pelecard, Tranzila, Meshulam, YaadPay, iCredit Rivhit, Nedarim Plus, Jaffa, Jaffa Bit, Tranzila Bit, Meshulam Bit, Gama, PayMe, HK Bank Transfer, Israel Toremet

**Latin America:**

* ASAAS (Brazil), MercadoPago (Mexico, Argentina, Chile), DLocal

**Europe:**

* Checkout.fi (Finland)

**South Africa:**

* PayFast, PayGate

**India:**

* Razorpay

**International:**

* Stripe, PayPal, Payarc

### By Payment Method

**Credit Card:**

* Most gateways support credit card processing

**ACH/Bank Transfer:**

* Cardknox (check), HK Bank Transfer

**Cryptocurrency:**

* Coinbase

**DAF/Vouchers:**

* Peach

**Campaign-Specific:**

* Achisomoch, Kol Yom, Tevini, Asser Bishvil, CMZ

***

## Common Fields

Most gateways share these common optional fields:

| Field                     | Type    | Description                                       |
| ------------------------- | ------- | ------------------------------------------------- |
| `legal_entity_id`         | Integer | ID of the legal entity for tax receipts           |
| `currency`                | String  | Primary currency code (e.g., "USD", "ILS", "EUR") |
| `status`                  | Boolean | Whether the gateway is active                     |
| `available_currency_list` | Array   | List of supported currencies                      |

***

## Best Practices

## 1. Choose the Right Gateway

Consider:

* **Geographic location** of your donors
* **Preferred payment methods** (credit card, bank transfer, crypto)
* **Currency support**
* **Transaction fees**
* **Compliance requirements**

## 2. Configure Multiple Gateways

For international organizations:

* Use **Stripe** or **PayPal** for international credit cards
* Add **local gateways** for specific regions (e.g., CardCom for Israel)
* Include **alternative methods** (e.g., Coinbase for crypto, Peach for DAF)

## 3. Link to Legal Entities

Always specify `legal_entity_id` to ensure:

* Correct tax receipts are issued
* Compliance with local regulations
* Proper accounting and reporting

## 4. Test Before Going Live

Most gateways support test/sandbox modes:

* Verify credentials work correctly
* Test the donation flow
* Confirm receipt generation

***

## 3. Gateway Status Check (Stripe)

Check the status of a specific gateway connection (e.g., Stripe Onboarding status).

**Endpoint:** `GET /orgarea/api/v1/organization/{orgId}/gateways`

**Query Parameters:**

| Parameter | Value    | Description                |
| --------- | -------- | -------------------------- |
| `gateway` | `stripe` | The gateway code to check. |

**Example Request:** `GET /orgarea/api/v1/organization/10001/gateways?gateway=stripe`

> **Warning:** Note This endpoint typically checks the connection status or onboarding progress for gateways that require OAuth or complex setup (like Stripe Connect). The response structure may vary depending on the gateway's state.

***

## Related Documentation

* [Donations](../campaigns/donations.md) - Process donations through configured gateways
* [Reference Data](../getting-started/reference.md) - Currency codes and country information
