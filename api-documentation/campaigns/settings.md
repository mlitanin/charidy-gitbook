# Settings & Meta

Campaign settings control specific behaviors and features, such as email receipts, team leader notifications, and display options. These are managed via the generic **Settings API** using the `meta` resource.

This system allows for flexible configuration without changing the core campaign schema for every new feature.

## Campaign Lifecycle & Statuses

Campaigns progress through different statuses, each with customizable descriptions and behaviors:

1. **Default** - Initial state before campaign starts
2. **Countdown Mode** - Pre-campaign countdown period (before `campaign_start_date`)
3. **Primary Goal** - Active campaign working toward the primary goal
4. **Bonus Goal** - Primary goal reached, working toward bonus goal
5. **Completed** - Campaign ended (after `campaign_end_date`)

> **Tip:** Custom Descriptions Each status can have its own custom title and description in multiple languages. If not defined, the default campaign description is shown.

## Important Dates

Campaigns have three critical timestamps:

* **Campaign Start Date** - When the campaign officially begins (countdown reaches 0)
* **Campaign End Date** - When the countdown clock reaches 0 (campaign "ends")
* **Accept Donations Until** - Final date for accepting donations (can be after end date)

> **Warning:** Timezone Awareness All dates are stored and displayed in the organization's timezone (e.g., `Asia/Jerusalem`). The dashboard shows: "You timezone set to {timezone}"

## Endpoints

| Method | Endpoint & Description                                                                                                                                     |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`  | <p><code>/organization/{orgId}/campaign/{id}/setting?meta_name={name}</code><br><strong>Get Setting.</strong> Retrieve a specific setting by its name.</p> |
| `POST` | <p><code>/organization/{orgId}/campaign/{id}/setting</code><br><strong>Create/Update Setting.</strong> Enable or configure a setting.</p>                  |
| `PUT`  | <p><code>/organization/{orgId}/campaign/{id}/setting</code><br><strong>Update Setting.</strong> Modify an existing setting.</p>                            |

## How It Works

Each setting is a `campaign_meta` object with a unique `meta_name`. The actual configuration value is stored in `meta_data` as a **JSON string**.

### Standard Request Format

When updating a setting, you send the `meta_name` key and the `meta_data` value (usually `{"value": true}` or `{"value": false}`).

**Example: turning ON email receipts**

```json
{
  "data": {
    "type": "campaign_meta",
    "attributes": {
      "campaign_id": 45788,
      "meta_name": "attach_pdf_to_receipt_email",
      "meta_data": "{\"value\":true}"
    }
  }
}
```

### Standard Response Format

```json
{
  "data": {
    "type": "campaign_meta",
    "id": "1057351",
    "attributes": {
      "campaign_id": 45788,
      "meta_name": "attach_pdf_to_receipt_email",
      "meta_data": "{\"value\":true}"
    }
  }
}
```

### Sending Complex Meta Configurations

For complex settings with multiple fields, you construct a JSON object with all the required fields, then **stringify it** into the `meta_data` field.

**Example: Configuring Team Leader Notifications**

**Step 1:** Build the configuration object:

```json
{
  "value": true,
  "allow_team_leader_respond": true,
  "skip_offline_donations": false,
  "template_id": 0
}
```

**Step 2:** Stringify it and send in the request:

```json
{
  "data": {
    "type": "campaign_meta",
    "attributes": {
      "campaign_id": 45788,
      "meta_name": "notify_team_leaders_on_each_donation",
      "meta_data": "{\"value\":true,\"allow_team_leader_respond\":true,\"skip_offline_donations\":false,\"template_id\":0}"
    }
  }
}
```

**Important Notes:**

* The `meta_data` value is **always a string**, even for complex objects
* You must escape quotes inside the JSON string (`\"` instead of `"`)
* All fields shown in the "Meta Data Structure" for each setting should be included

***

## Quick Reference: All Settings

Below is a comprehensive list of all available `meta_name` keys. Most accept a simple boolean value (`true`/`false`), while others have complex configurations detailed in dedicated sections below.

### Receipt & Email Settings

| Meta Name                     | Description                                                                     | Type    |
| ----------------------------- | ------------------------------------------------------------------------------- | ------- |
| `attach_pdf_to_receipt_email` | Attaches a formal PDF receipt to the donor's email (includes legal entity info) | Boolean |
| `do_not_send_email_receipt`   | Completely disables receipt emails (useful if gateway sends receipts)           | Boolean |

> **Tip:** Email Language Receipt emails are sent in the language configured in Organization Settings → "Email correspondence language" (e.g., עברית, English).

### Team Settings

| Meta Name                                    | Description                                                                         | Type                                                              |
| -------------------------------------------- | ----------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `allow_team_update_by_public_token`          | Allows teams to be updated via a public link without login (useful for ambassadors) | Boolean                                                           |
| `add_team_hidden_by_default`                 | New teams are hidden from the public list until approved by admin                   | Boolean                                                           |
| `allow_access_hidden_team_by_link`           | Hidden teams can still be accessed via direct link (for private fundraising)        | Boolean                                                           |
| `team_page_hide_donor_list`                  | Hides the donor list on team pages (privacy setting)                                | [Complex](settings.md#team-page-hide-donor-list)                  |
| `notify_team_leaders_on_each_donation`       | Sends email alerts to team leaders for each donation to their team                  | [Complex](settings.md#notify-team-leaders-on-each-donation)       |
| `team_donation_notification_by_sms`          | Sends SMS notifications to team leaders (requires SMS credits)                      | [Complex](settings.md#team-donation-notification-by-sms)          |
| `send_sms_to_team_leaders_when_team_created` | Sends SMS when a team is created (welcome message)                                  | [Complex](settings.md#send-sms-to-team-leaders-when-team-created) |
| `teams_config`                               | Advanced team display configuration (sorting, filtering, featured teams)            | [Complex](settings.md#teams-config)                               |
| `featured_teams`                             | Controls which teams appear in the "Featured Teams" section                         | [Complex](settings.md#featured-teams)                             |

### Donation Form Settings

| Meta Name                     | Description                                                                | Type    |
| ----------------------------- | -------------------------------------------------------------------------- | ------- |
| `show_billing_address`        | Shows billing address fields in donation form (required for some gateways) | Boolean |
| `phone_required`              | Makes phone number mandatory in donation form                              | Boolean |
| `donation_form_custom_fields` | Adds custom fields to donation form (e.g., "Dedication", "In Honor Of")    | Object  |
| `include_fee_to_my_donaton`   | Allows donors to cover processing fees (increases donation amount)         | Boolean |
| `gateway_fee`                 | Configures payment gateway fees (percentage + fixed amount)                | Object  |

### Donation Limits & Rules

| Meta Name                     | Description                                                          | Type                                           |
| ----------------------------- | -------------------------------------------------------------------- | ---------------------------------------------- |
| `maximum_donation_amount`     | Sets maximum donation limits per currency (anti-fraud measure)       | [Complex](settings.md#maximum-donation-amount) |
| `min_donation_amount`         | Sets minimum donation amount (e.g., $10 minimum)                     | [Complex](settings.md#min-donation-amount)     |
| `place_new_donations_on_hold` | Places all new donations on hold for manual review before processing | Boolean                                        |
| `recurring_payments`          | Enables recurring/subscription donations (monthly, yearly)           | Object                                         |

### Donation Levels & Display

| Meta Name                           | Description                                  | Type    |
| ----------------------------------- | -------------------------------------------- | ------- |
| `show_donation_levels_above_amount` | Shows donation levels above the amount field | Boolean |
| `data_2_columns`                    | Forces a two-column layout for data displays | Boolean |

### Post-Donation Settings

| Meta Name                      | Description                              | Type                                                |
| ------------------------------ | ---------------------------------------- | --------------------------------------------------- |
| `donation_success_page_config` | Customizes the donation success page     | [Complex](settings.md#donation-success-page-config) |
| `donation_upsell`              | Shows upsell options after donation      | Object                                              |
| `donor_rescue_notification`    | Sends notifications for failed donations | [Complex](settings.md#donor-rescue-notification)    |

### Campaign Display Settings

| Meta Name               | Description                                                              | Type                                         |
| ----------------------- | ------------------------------------------------------------------------ | -------------------------------------------- |
| `hide_goal`             | Hides the campaign goal amount from public view                          | Boolean                                      |
| `hideclock`             | Hides the countdown timer (useful for evergreen campaigns)               | Boolean                                      |
| `hide_donor_list`       | Hides the public donor list (privacy mode)                               | Boolean                                      |
| `donors_goal`           | Sets a goal for number of donors (e.g., "500 donors")                    | [Complex](settings.md#donors-goal)           |
| `projector_mode`        | Optimizes display for projector/large screens (bigger fonts, animations) | [Complex](settings.md#projector-mode)        |
| `activate_confetti_now` | Triggers confetti animation (celebration effect)                         | [Complex](settings.md#activate-confetti-now) |

> **Tip:** Custom Progress Bar Note You can add a custom note under the progress bar in multiple languages via the dashboard: "Custom note under the progress bar" field.

### Integration & Tracking

| Meta Name            | Description                                          | Type   |
| -------------------- | ---------------------------------------------------- | ------ |
| `google_tag_manager` | Google Tag Manager container ID (e.g., "GTM-546578") | Object |
| `campaign_colors`    | Sets custom brand colors (primary, secondary)        | Object |

> **Tip:** Additional Tracking Fields The following tracking fields are configured in the "General Information" section of the campaign dashboard:

* **Google Analytics ID** - Format: `G-XXXXXXX` (e.g., "G-7665656")
* **Facebook Pixel ID** - Numeric ID (e.g., "86676869876")
* **Google AdWords Conversion ID** - Format: `AW-XXXXXXX`
* **Google AdWords Conversion Label** - Custom label for conversion tracking
* **Google Tag Manager** - Format: `GTM-XXXXXX`

These fields enable comprehensive tracking of campaign performance, donor behavior, and conversion metrics.

### Advanced Settings

| Meta Name                          | Description                               | Type   |
| ---------------------------------- | ----------------------------------------- | ------ |
| `notify_org_about_donations_above` | Notifies org admins for large donations   | Object |
| `pre_donation_pdf_form`            | Requires PDF form before donation         | Object |
| `pre_campaign_team_event`          | Pre-campaign team event configuration     | Object |
| `ticket_free_price`                | Allows free tickets                       | Object |
| `unidy_tabs_order`                 | Controls tab order in Unidy template      | Object |
| `unidy_classic_tabs_vue`           | Unidy classic tabs configuration          | Object |
| `add_team_from_campaign`           | Controls team creation from campaign page | Object |

***

## Detailed Configuration: Complex Settings

The following settings require more than a simple boolean value. Each has its own structure and validation rules.

### team\_page\_hide\_donor\_list

Hides the donor list on team pages, with optional exceptions for specific teams.

**Meta Data Structure:**

```json
{
  "value": true,
  "team_id_exception": [1449278, 1449279]
}
```

**Fields:**

* `value` (boolean): `true` to hide donor lists globally
* `team_id_exception` (array): List of team IDs that should still show their donor lists

**Example Request:**

```json
{
  "data": {
    "type": "campaign_meta",
    "attributes": {
      "campaign_id": 45788,
      "meta_name": "team_page_hide_donor_list",
      "meta_data": "{\"value\":true,\"team_id_exception\":[1449278]}"
    }
  }
}
```

***

### notify\_team\_leaders\_on\_each\_donation

Sends an email notification to team leaders whenever their team receives a donation.

**Meta Data Structure:**

```json
{
  "value": true,
  "allow_team_leader_respond": true,
  "skip_offline_donations": true,
  "template_id": 0
}
```

**Fields:**

* `value` (boolean): Enable/disable the feature
* `allow_team_leader_respond` (boolean): Include a reply-to address so leaders can respond
* `skip_offline_donations` (boolean): Don't send notifications for offline donations
* `template_id` (integer): Custom email template ID (0 = default template)

***

### team\_donation\_notification\_by\_sms

Sends SMS notifications to team leaders when donations are received.

**Meta Data Structure:**

```json
{
  "value": true,
  "sms_template": "New donation of {amount} received for your team!"
}
```

**Fields:**

* `value` (boolean): Enable/disable SMS notifications
* `sms_template` (string): Custom SMS message template. Supports variables like `{amount}`, `{donor_name}`, etc.

***

### send\_sms\_to\_team\_leaders\_when\_team\_created

Sends an SMS to team leaders when their team is created.

**Meta Data Structure:**

```json
{
  "value": true,
  "on_team_import": true,
  "on_team_created_from_dashboard": true,
  "on_team_created_from_campaign": false
}
```

**Fields:**

* `value` (boolean): Master toggle for the feature
* `on_team_import` (boolean): Send SMS when teams are bulk imported
* `on_team_created_from_dashboard` (boolean): Send SMS when created via admin dashboard
* `on_team_created_from_campaign` (boolean): Send SMS when created from the campaign page

***

### teams\_config

Advanced configuration for team display and filtering.

**Meta Data Structure:**

```json
{
  "value": true,
  "skip_without_goal_and_donations": true,
  "skip_without_donations": true,
  "use_team_description": false
}
```

**Fields:**

* `value` (boolean): Enable advanced team filtering
* `skip_without_goal_and_donations` (boolean): Hide teams with no goal AND no donations
* `skip_without_donations` (boolean): Hide teams with no donations
* `use_team_description` (boolean): Display team descriptions in listings

***

### maximum\_donation\_amount

Sets maximum donation limits per currency.

**Meta Data Structure:**

```json
{
  "value": true,
  "currency_to_amount": {
    "USD": 10000,
    "ILS": 36000,
    "EUR": 9000
  }
}
```

**Fields:**

* `value` (boolean): Enable maximum donation limits
* `currency_to_amount` (object): Map of currency codes to maximum amounts

***

### min\_donation\_amount

Sets the minimum donation amount allowed.

**Meta Data Structure:**

```json
{
  "value": true,
  "amount": 50
}
```

**Fields:**

* `value` (boolean): Enable minimum donation requirement
* `amount` (number): Minimum amount in the campaign's base currency

***

### donation\_success\_page\_config

Customizes the donation success/thank you page.

**Meta Data Structure:**

```json
{
  "value": true,
  "custom_message": "<p>Thank you for your generous donation!</p>",
  "banner_image": {
    "url": "https://cdn.charidy.com/images/thank-you-banner.jpg"
  }
}
```

**Fields:**

* `value` (boolean): Enable custom success page
* `custom_message` (string): HTML content for custom thank you message
* `banner_image` (object): Banner image configuration
  * `url` (string): Full URL to the banner image

***

### donor\_rescue\_notification

Sends notifications when donations fail (e.g., credit card declined).

**Meta Data Structure:**

```json
{
  "value": true,
  "do_not_sent_to_team_leader": false,
  "do_not_sent_to_donor": false,
  "do_not_sent_to_org": true
}
```

**Fields:**

* `value` (boolean): Enable donor rescue notifications
* `do_not_sent_to_team_leader` (boolean): Exclude team leaders from notifications
* `do_not_sent_to_donor` (boolean): Exclude the donor from retry notifications
* `do_not_sent_to_org` (boolean): Exclude organization admins from notifications

***

### featured\_teams

Controls how featured teams are displayed on the campaign page.

**Meta Data Structure:**

```json
{
  "value": true,
  "place": "parent",
  "where_to_show": "under_progress_bar",
  "donate_team_sort_by_featured": true,
  "extra_params": {}
}
```

**Fields:**

* `value` (boolean): Enable featured teams display
* `place` (string): Which teams to feature (`"parent"`, `"all"`, `"top_level"`)
* `where_to_show` (string): Display location (`"under_progress_bar"`, `"teams_tab"`, `"sidebar"`)
* `donate_team_sort_by_featured` (boolean): Sort teams by featured status
* `extra_params` (object): Additional custom parameters

***

### activate\_confetti\_now

Triggers a confetti animation on the campaign page.

**Meta Data Structure:**

```json
{
  "value": true,
  "confetti_type": "default"
}
```

**Fields:**

* `value` (boolean): Activate confetti animation
* `confetti_type` (string): Animation style (`"default"`, `"fireworks"`, `"celebration"`)

***

### donors\_goal

Sets a goal for the number of donors (separate from the monetary goal).

**Meta Data Structure:**

```json
{
  "value": true,
  "amount": 1000,
  "show_total_raised": true,
  "show_total": true,
  "campaign": true,
  "hide_team_amount": false
}
```

**Fields:**

* `value` (boolean): Enable donor count goal
* `amount` (number): Target number of donors
* `show_total_raised` (boolean): Display total amount raised alongside donor count
* `show_total` (boolean): Show total donor count
* `campaign` (boolean): Apply to campaign level (vs team level)
* `hide_team_amount` (boolean): Hide team-specific amounts

***

### projector\_mode

Optimizes the campaign display for projectors and large screens (e.g., at events).

**Meta Data Structure:**

```json
{
  "value": true,
  "show_level_instead_amount": true
}
```

**Fields:**

* `value` (boolean): Enable projector mode
* `show_level_instead_amount` (boolean): Display donation level names instead of amounts for privacy
