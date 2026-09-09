# Overview

Manage your personal user profile, security settings, and preferences.

## Endpoints

| Method  | Endpoint & Description                                                                                           |
| ------- | ---------------------------------------------------------------------------------------------------------------- |
| `GET`   | <p><code>/signin</code><br><strong>Get Profile.</strong> Retrieve current user details including 2FA status.</p> |
| `PATCH` | <p><code>/account/donor_account</code><br><strong>Update Profile.</strong> Update name and email.</p>            |
| `POST`  | <p><code>/account/password</code><br><strong>Change Password.</strong> Update your login password.</p>           |
| `GET`   | <p><code>/account/2fa</code><br><strong>Setup 2FA.</strong> Get the QR code for enabling 2FA.</p>                |

***

## 1. Get User Profile

Retrieve details about the currently logged-in user.

**Endpoint:** `GET /orgarea/api/v1/signin`

**Response:**

```json
{
  "data": {
    "type": "account",
    "id": "242907",
    "attributes": {
      "email": "user@example.com",
      "phone": "+15551234567",
      "twofa_active": false
    }
  }
}
```

***

## 2. Update Profile

Update your personal information.

**Endpoint:** `PATCH /orgarea/api/v1/account/donor_account`

**Request Body:**

```json
{
  "data": {
    "attributes": {
      "first_name": "Shneor",
      "last_name": "Cohen",
      "email": "new.email@example.com"
    }
  }
}
```

***

## 3. Change Password

Update your login password.

**Endpoint:** `POST /orgarea/api/v1/account/password`

**Request Body:**

```json
{
  "data": {
    "attributes": {
      "email": "user@example.com",
      "current_password": "OldPassword123!",
      "new_password": "NewPassword123$",
      "new_password2": "NewPassword123$"
    }
  }
}
```

### Password Requirements

The new password must meet the following security criteria:

* At least **8 characters** long.
* Include at least **one capital letter**.
* Include at least **one number**.
* Include at least **one special symbol**.

### Attributes

| Attribute          | Type   | Description                       |
| ------------------ | ------ | --------------------------------- |
| `current_password` | String | Your existing password.           |
| `new_password`     | String | The new password.                 |
| `new_password2`    | String | Confirmation of the new password. |

***

## 4. Two-Factor Authentication (2FA)

Retrieve the QR code to set up Two-Factor Authentication.

**Endpoint:** `GET /orgarea/api/v1/account/2fa`

**Response:**

```json
{
  "qr_code": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..."
}
```

The response contains a Base64 encoded string of the QR code image. This string should be rendered as an image (src="data:image/png;base64,...") and scanned by an authenticator app (like Google Authenticator) to generate a verification code.
