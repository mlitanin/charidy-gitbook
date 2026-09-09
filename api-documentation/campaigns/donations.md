# Donations

Donations are the core transactions of your campaign. This API allows you to retrieve donation records, create offline donations, and manage donation assignments to teams.

## Endpoints

| Method | Endpoint & Description                                                                                                                                                                |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`  | <p><code>/organization/{orgId}/campaign/{id}/donations</code><br><strong>List Donations.</strong> Retrieve all donations for a campaign with filtering and pagination.</p>            |
| `POST` | <p><code>/organization/{orgId}/campaign/{id}/donations</code><br><strong>Create Offline Donation.</strong> Manually record a donation (cash, check, etc.).</p>                        |
| `POST` | <p><code>/organization/{orgId}/campaign/{id}/donation/{donationId}/campaign_team_donation</code><br><strong>Assign to Team.</strong> Associate a donation with one or more teams.</p> |

***

## 1. List Donations

Retrieve all donations for a specific campaign with optional filtering, sorting, and pagination.

**Endpoint:** `GET /organization/{orgId}/campaign/{id}/donations`

**Query Parameters:**

| Parameter | Type    | Description                                                                            |
| --------- | ------- | -------------------------------------------------------------------------------------- |
| `page`    | Integer | Page number for pagination (default: 1).                                               |
| `limit`   | Integer | Number of results per page (default: 50, max: 100).                                    |
| `extend`  | String  | Include related data. Use `extend=donation_receipt_id` to include receipt information. |
| `loc`     | String  | Language code for localized content (e.g., `en`, `he`).                                |

**Example Request:**

```http
GET /organization/10001/campaign/45788/donations?page=1&limit=50&extend=donation_receipt_id&loc=en
```

**Response Structure:**

The response includes a `data` array of donation objects and an `included` array with related resources (teams, campaign data, selected levels).

```json
{
  "data": [
    {
      "type": "donation",
      "id": "18389886",
      "attributes": {
        "campaign_id": 45788,
        "campaign_name": "Campaign title test",
        "display_name": "John Doe",
        "email": "donor@example.com",
        "phone": "050-1234567",
        "effective_amount": 180,
        "charged_amount": 90,
        "currency_code": "usd",
        "currency_sign": "$",
        "status": "Processed",
        "date": 1767469228,
        "effective_date": 1767448800,
        "bank_name": "stripe",
        "donation_type": "D",
        "dedication": "In memory of...",
        "category": "General",
        "tag": "featured",
        "team_id_list": [1449278],
        "payment_address": "123 Main St",
        "payment_city": "New York",
        "payment_state": "NY",
        "payment_country": "US",
        "payment_postcode": "10001",
        "offline_donation_source": "",
        "offline_donation_note": "",
        "send_receipt": true,
        "can_generate_receipt": true
      },
      "relationships": {
        "campaign": {
          "data": { "type": "campaign", "id": "45788" }
        },
        "campaign_team_donations": {
          "data": [
            { "type": "campaign_team_donation", "id": "9826773" }
          ]
        },
        "selected_levels": {
          "data": [
            { "type": "selected_level", "id": "80142" }
          ]
        }
      }
    }
  ],
  "included": [
    {
      "type": "campaign_team_donation",
      "id": "9826773",
      "attributes": {
        "campaign_id": 45788,
        "team_id": 1449278,
        "amount": 9000,
        "real_payment": 9000,
        "total": 18000
      }
    },
    {
      "type": "selected_level",
      "id": "80142",
      "attributes": {
        "count": 1
      }
    }
  ]
}
```

**Response Headers:**

| Header               | Description                                    |
| -------------------- | ---------------------------------------------- |
| `x-total-donations`  | Total number of donations matching the query.  |
| `x-search-donations` | Number of donations returned in this response. |

***

## 2. Create Offline Donation

Manually record a donation that was received outside the platform (cash, check, bank transfer, etc.).

**Endpoint:** `POST /organization/{orgId}/campaign/{id}/donations`

**Request Attributes:**

| Attribute                     | Type    | Required | Description                                                                                                                                                                                                              |
| ----------------------------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **`campaign_id`**             | Integer | Yes      | The campaign ID receiving the donation.                                                                                                                                                                                  |
| **`effective_amount`**        | Number  | Yes      | The total donation amount (after matching/multipliers).                                                                                                                                                                  |
| **`charged_amount`**          | Number  | Yes      | The actual amount paid by the donor.                                                                                                                                                                                     |
| **`currency_code`**           | String  | Yes      | Currency code (e.g., `usd`, `ils`, `eur`).                                                                                                                                                                               |
| **`bank_name`**               | String  | Yes      | Payment method. Use `"check"` for offline donations.                                                                                                                                                                     |
| **`display_name`**            | String  | Yes      | Donor's display name.                                                                                                                                                                                                    |
| **`email`**                   | String  | Yes      | Donor's email address.                                                                                                                                                                                                   |
| **`phone`**                   | String  | No       | Donor's phone number.                                                                                                                                                                                                    |
| **`billing_name`**            | String  | No       | Billing first name.                                                                                                                                                                                                      |
| **`billing_last_name`**       | String  | No       | Billing last name.                                                                                                                                                                                                       |
| **`dedication`**              | String  | No       | Dedication or tribute message.                                                                                                                                                                                           |
| **`category`**                | String  | No       | Donation category or designation.                                                                                                                                                                                        |
| **`tag`**                     | String  | No       | Special tag (e.g., `"featured"` to highlight the donation).                                                                                                                                                              |
| **`team_id_list`**            | Array   | No       | List of team IDs to assign this donation to.                                                                                                                                                                             |
| **`offline_donation_source`** | String  | No       | Source: `cash`, `credit_card`, `check`, `bank_transfer`, `recurring_credit_card`, `recurring_bank_transfer`, `charity_voucher`, `pledge`, `fund`, `crm_donations`, `loan`, `sms`, `paypal`, `fidelity`, `zelle`, `other` |
| **`offline_donation_note`**   | String  | No       | Internal note about the offline donation.                                                                                                                                                                                |
| **`payment_address`**         | String  | No       | Donor's street address.                                                                                                                                                                                                  |
| **`payment_address_2`**       | String  | No       | Additional address line.                                                                                                                                                                                                 |
| **`payment_city`**            | String  | No       | City.                                                                                                                                                                                                                    |
| **`payment_state`**           | String  | No       | State/Province.                                                                                                                                                                                                          |
| **`payment_country`**         | String  | No       | Country code (e.g., `"US"`, `"IL"`).                                                                                                                                                                                     |
| **`payment_postcode`**        | String  | No       | Postal/ZIP code.                                                                                                                                                                                                         |
| **`effective_date`**          | Integer | No       | Unix timestamp for when the donation should be counted (default: current time).                                                                                                                                          |
| **`send_receipt`**            | Boolean | No       | Whether to send a receipt email (default: false).                                                                                                                                                                        |
| **`selected_levels`**         | Array   | No       | Array of donation level objects: `[{"level_id": 123, "count": 1}]`.                                                                                                                                                      |

**Example Request:**

```json
{
  "data": {
    "type": "donation",
    "attributes": {
      "campaign_id": 45788,
      "effective_amount": 180,
      "charged_amount": 90,
      "currency_code": "usd",
      "bank_name": "check",
      "display_name": "Sarah Cohen",
      "billing_name": "Sarah",
      "billing_last_name": "Cohen",
      "email": "sarah@example.com",
      "phone": "555-1234",
      "dedication": "In honor of my parents",
      "category": "Education Fund",
      "tag": "featured",
      "team_id_list": [1449278],
      "offline_donation_source": "cash",
      "offline_donation_note": "Received at event",
      "payment_address": "456 Oak Avenue",
      "payment_city": "Brooklyn",
      "payment_state": "NY",
      "payment_country": "US",
      "payment_postcode": "11201",
      "effective_date": 1767448800,
      "send_receipt": false,
      "selected_levels": [
        {
          "level_id": 80142,
          "count": 1
        }
      ]
    }
  }
}
```

**Response:**

Returns the created donation object with the same structure as the GET response.

***

## 3. Assign Donation to Team

Associate an existing donation with one or more teams. This is useful when you need to split a donation across multiple teams or reassign it.

**Endpoint:** `POST /organization/{orgId}/campaign/{id}/donation/{donationId}/campaign_team_donation`

**Request Attributes:**

| Attribute        | Type    | Required | Description                                                           |
| ---------------- | ------- | -------- | --------------------------------------------------------------------- |
| **`team_id`**    | Integer | Yes      | The team ID to assign the donation to.                                |
| **`amount`**     | Number  | Yes      | The portion of the donation to assign to this team (in cents/agorot). |
| **`dedication`** | String  | No       | Team-specific dedication message.                                     |

**Example Request:**

```json
{
  "data": {
    "type": "campaign_team_donation",
    "attributes": {
      "team_id": 1449278,
      "amount": 9000,
      "dedication": ""
    }
  }
}
```

**Response:**

```json
{
  "data": {
    "type": "campaign_team_donation",
    "id": "9826773",
    "attributes": {
      "campaign_id": 45788,
      "team_id": 1449278,
      "amount": 9000,
      "real_payment": 9000,
      "total": 18000,
      "dedication": ""
    }
  }
}
```

***

## Donation Object Reference

### Core Fields

| Field                       | Type    | Description                                                     |
| --------------------------- | ------- | --------------------------------------------------------------- |
| `id`                        | String  | Unique donation ID.                                             |
| `campaign_id`               | Integer | Associated campaign ID.                                         |
| `campaign_name`             | String  | Campaign title.                                                 |
| `display_name`              | String  | Donor's public display name.                                    |
| `email`                     | String  | Donor's email address.                                          |
| `phone`                     | String  | Donor's phone number.                                           |
| `effective_amount`          | Number  | Total donation amount (after matching/multipliers).             |
| `charged_amount`            | Number  | Actual amount charged to the donor.                             |
| `processing_charged_amount` | Number  | Amount processed by payment gateway.                            |
| `fee_cover_amount`          | Number  | Amount donor covered for processing fees.                       |
| `currency_code`             | String  | Currency code (e.g., `usd`, `ils`).                             |
| `currency_sign`             | String  | Currency symbol (e.g., `$`, `₪`).                               |
| `status`                    | String  | Donation status (`"Processed"`, `"Pending"`, `"Failed"`, etc.). |
| `date`                      | Integer | Unix timestamp when donation was created.                       |
| `effective_date`            | Integer | Unix timestamp when donation should be counted.                 |
| `update_date`               | Integer | Unix timestamp of last update.                                  |

### Payment Information

| Field             | Type    | Description                                                           |
| ----------------- | ------- | --------------------------------------------------------------------- |
| `bank_name`       | String  | Payment gateway/method (e.g., `stripe`, `check`, `paypal`).           |
| `bank_name_label` | String  | Human-readable payment method label.                                  |
| `transaction_id`  | String  | Gateway transaction ID.                                               |
| `donation_type`   | String  | Type of donation (`"D"` = direct, `"P"` = pledge, `"R"` = recurring). |
| `captured`        | Boolean | Whether payment was captured.                                         |
| `failure_reason`  | String  | Reason for payment failure (if applicable).                           |

### Donor Information

| Field               | Type   | Description              |
| ------------------- | ------ | ------------------------ |
| `billing_name`      | String | Billing first name.      |
| `billing_last_name` | String | Billing last name.       |
| `payment_address`   | String | Street address.          |
| `payment_address_2` | String | Additional address line. |
| `payment_city`      | String | City.                    |
| `payment_state`     | String | State/Province.          |
| `payment_country`   | String | Country code.            |
| `payment_postcode`  | String | Postal/ZIP code.         |

### Dedication & Categorization

| Field        | Type   | Description                                                 |
| ------------ | ------ | ----------------------------------------------------------- |
| `dedication` | String | Dedication or tribute message.                              |
| `category`   | String | Donation category/designation.                              |
| `tag`        | String | Special tag (e.g., `"featured"` for highlighted donations). |

### Team Assignment

| Field          | Type    | Description                                                 |
| -------------- | ------- | ----------------------------------------------------------- |
| `team_id`      | Integer | Primary team ID (legacy field, use `team_id_list` instead). |
| `team_id_list` | Array   | List of all team IDs this donation is assigned to.          |

### Offline Donations

| Field                     | Type   | Description                                                                                                                                                                                                              |
| ------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `offline_donation_source` | String | Source: `cash`, `credit_card`, `check`, `bank_transfer`, `recurring_credit_card`, `recurring_bank_transfer`, `charity_voucher`, `pledge`, `fund`, `crm_donations`, `loan`, `sms`, `paypal`, `fidelity`, `zelle`, `other` |
| `offline_donation_note`   | String | Internal note about the offline donation.                                                                                                                                                                                |
| `check_n`                 | String | Check number (if applicable).                                                                                                                                                                                            |
| `account`                 | String | Bank account number (for bank transfers).                                                                                                                                                                                |
| `bank`                    | String | Bank name.                                                                                                                                                                                                               |
| `branch`                  | String | Bank branch.                                                                                                                                                                                                             |

### Recurring Donations

| Field                   | Type    | Description                                    |
| ----------------------- | ------- | ---------------------------------------------- |
| `recurring_period`      | Integer | Billing period in days (e.g., 30 for monthly). |
| `recurring_canceled`    | Boolean | Whether recurring donation was canceled.       |
| `recurring_canceled_by` | String  | Who canceled the recurring donation.           |
| `recurring_paid_off`    | Boolean | Whether recurring donation is fully paid.      |
| `unlimited_sub`         | Boolean | Whether subscription has no end date.          |
| `sub_duration`          | Integer | Subscription duration.                         |
| `sub_duration_type`     | String  | Duration type (e.g., `"months"`, `"years"`).   |
| `sub_type`              | String  | Subscription type.                             |
| `installments_n`        | Integer | Number of installments.                        |

### Receipt & Communication

| Field                                       | Type    | Description                         |
| ------------------------------------------- | ------- | ----------------------------------- |
| `send_receipt`                              | Boolean | Whether to send receipt email.      |
| `send_receipt_without_pdf_receipt_attached` | Boolean | Send email without PDF attachment.  |
| `send_confirmation_email`                   | Boolean | Send confirmation email to donor.   |
| `donation_receipt_id`                       | Integer | Associated receipt ID.              |
| `receipt_name`                              | String  | Name to appear on receipt.          |
| `can_generate_receipt`                      | Boolean | Whether a receipt can be generated. |

### Matching & Pledges

| Field                             | Type    | Description                                                       |
| --------------------------------- | ------- | ----------------------------------------------------------------- |
| `matched_donation_id`             | Integer | ID of matched donation (if this is a matcher's contribution).     |
| `peer_matched_donation_id`        | Integer | ID of peer-matched donation.                                      |
| `peer_matched_by_donation_id`     | Integer | ID of donation that peer-matched this one.                        |
| `pledge_with_notification_to_pay` | Boolean | Whether pledge has payment notification.                          |
| `pledge_notification_to_pay_sent` | Boolean | Whether payment notification was sent.                            |
| `successful_donation_id`          | Integer | ID of successful retry (if this donation failed and was retried). |

### Administrative

| Field               | Type    | Description                              |
| ------------------- | ------- | ---------------------------------------- |
| `created_by`        | String  | Email of user who created the donation.  |
| `customer_id`       | Integer | Donor's customer ID in the system.       |
| `legal_entity_id`   | Integer | Associated legal entity ID.              |
| `org_comment`       | String  | Internal organization comment.           |
| `moderation_status` | Integer | Moderation status (1 = approved).        |
| `locked`            | Boolean | Whether donation is locked from editing. |
| `locked_type`       | String  | Type of lock applied.                    |
| `can_be_canceled`   | Boolean | Whether donation can be canceled.        |
| `lead_status`       | String  | Lead status for tracking.                |
| `referrer`          | String  | Referral source.                         |
| `stream_id`         | Integer | Associated donation stream ID.           |
| `module_data_id`    | Integer | Associated module data ID.               |

### Change Requests

| Field                             | Type    | Description                                |
| --------------------------------- | ------- | ------------------------------------------ |
| `change_request_type`             | String  | Type of change request.                    |
| `change_request_note`             | String  | Note about change request.                 |
| `mark_canceled_donation_id`       | Integer | ID of donation this one marks as canceled. |
| `mark_pledge_ref_as_cancelled_id` | Integer | ID of pledge reference marked as canceled. |
