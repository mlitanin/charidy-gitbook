# Authentication

The Charidy Dashboard API uses JWT (JSON Web Token) based authentication. Users can authenticate using email/password or phone number with SMS verification.

## Authentication Flow

1. **Login** - Obtain JWT token using email/password or phone number
2. **Use Token** - Include JWT token in `Authorization` header for all API requests
3. **Logout** - Invalidate the current session

> **Tip:** Multi-Organization Users If your user belongs to multiple organizations, see [Organizations](organizations.md) to learn how to list and select which organization to work with.

## Endpoints

| Method | Endpoint & Description                                                                      |
| ------ | ------------------------------------------------------------------------------------------- |
| `POST` | <p><code>/login</code><br>Authenticate User. Login with email/password or phone number.</p> |
| `GET`  | <p><code>/signin</code><br>Get Current User. Retrieve authenticated user information.</p>   |
| `POST` | <p><code>/logout</code><br>End Session. Logout and invalidate the current token.</p>        |

***

## 1. Login

Authenticate a user and receive a JWT token for subsequent API requests.

**Endpoint:** `POST /orgarea/api/v1/login`

## Authentication Methods

The API supports three authentication methods:

## Option A: Email & Password

**Request Body:**

```json
{
  "email": "user@example.com",
  "password": "your_password",
  "phone": ""
}
```

**Response:**

```json
{
  "data": {
    "type": "user",
    "id": "171549",
    "attributes": {
      "exp_date": 1768077366,
      "jwt_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "token": "61f6e8d8-6e86-4591-9e8b-393da808ecf6",
      "type": "O",
      "require_2fa_code": false,
      "phone_code_token": ""
    }
  }
}
```

## B. Phone Number (SMS Verification - Step 1)

Send a verification code to the user's phone number.

**Request Body:**

```json
{
  "phone": "+1 555 123 4567",
  "phone_code": "",
  "phone_code_token": ""
}
```

**Response:**

```json
{
  "data": {
    "type": "user",
    "id": "0",
    "attributes": {
      "exp_date": null,
      "jwt_token": "",
      "phone_code_token": "fiO-VTJ0-qYW9p6JzjgTA4Q2uR1tmc7W...",
      "token": "",
      "type": "",
      "require_2fa_code": false
    }
  }
}
```

> **Tip:** The `phone_code_token` is returned and must be used in Step 2 to verify the SMS code.

## C. Phone Number (SMS Verification - Step 2)

Verify the SMS code and complete authentication.

**Request Body:**

```json
{
  "phone": "+1 555 123 4567",
  "phone_code": "123456",
  "phone_code_token": "fiO-VTJ0-qYW9p6JzjgTA4Q2uR1tmc7W..."
}
```

**Response:**

```json
{
  "data": {
    "type": "user",
    "id": "242907",
    "attributes": {
      "exp_date": 1768077345,
      "jwt_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "token": "45cf4d7c-8e30-42d6-b7d6-3e784304b0df",
      "type": "O",
      "require_2fa_code": false,
      "phone_code_token": ""
    }
  }
}
```

### Response Attributes

| Attribute          | Type    | Description                                                     |
| ------------------ | ------- | --------------------------------------------------------------- |
| `id`               | String  | User ID.                                                        |
| `jwt_token`        | String  | **JWT token** to use in `Authorization: Bearer {token}` header. |
| `token`            | String  | Legacy token (use `jwt_token` instead).                         |
| `exp_date`         | Integer | Unix timestamp when the token expires.                          |
| `type`             | String  | User type (`"O"` for organization user).                        |
| `require_2fa_code` | Boolean | Whether 2FA is required (future use).                           |
| `phone_code_token` | String  | Temporary token for phone verification (only in Step 1).        |

***

## 2. Get Current User

Retrieve information about the currently authenticated user.

**Endpoint:** `GET /orgarea/api/v1/signin`

**Headers:**

```http
Authorization: Bearer {jwt_token}
```

**Response:**

```json
{
  "data": {
    "type": "account",
    "id": "242907",
    "attributes": {
      "email": "user@example.com",
      "phone": "+1 555 123 4567",
      "settings": {},
      "twofa_active": false
    }
  }
}
```

**Response Attributes:**

| Attribute      | Type    | Description                                   |
| -------------- | ------- | --------------------------------------------- |
| `id`           | String  | User account ID.                              |
| `email`        | String  | User's email address.                         |
| `phone`        | String  | User's phone number.                          |
| `settings`     | Object  | User-specific settings.                       |
| `twofa_active` | Boolean | Whether two-factor authentication is enabled. |

***

## 3. Logout

End the current session and invalidate the authentication token.

**Endpoint:** `POST /orgarea/api/v1/logout`

**Headers:**

```http
Authorization: Bearer {jwt_token}
```

**Response:**

```json
{
  "status": "success",
  "code": 200,
  "message": "logout successful"
}
```

***

## Using the JWT Token

Once you have obtained a JWT token from the login endpoint, include it in the `Authorization` header for all subsequent API requests:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Example Request

```bash
curl -X GET "https://dashboardapi.charidy.com/orgarea/api/v1/organizations" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Accept: application/json"
```

***

## Token Expiration

JWT tokens expire after a certain period (indicated by the `exp_date` field in the login response). When a token expires:

1. API requests will return a `401 Unauthorized` error
2. You must re-authenticate using the login endpoint to obtain a new token

> **Warning:** Store JWT tokens securely and never expose them in client-side code or version control.

***

## Error Responses

### Invalid Credentials

```json
{
  "error": "Invalid credentials",
  "code": 401
}
```

### Invalid Phone Code

```json
{
  "error": "Invalid verification code",
  "code": 400
}
```

### Expired Token

```json
{
  "error": "Token expired",
  "code": 401
}
```

***

## Rate Limiting

The login endpoint is rate-limited to prevent brute-force attacks:

* **Limit:** 5 requests per minute per IP address
* **Headers:** Rate limit information is returned in response headers:
  * `x-ratelimit-limit`: Maximum requests allowed
  * `x-ratelimit-remaining`: Requests remaining
  * `x-ratelimit-reset`: Unix timestamp when the limit resets

**Example Headers:**

```http
x-ratelimit-limit: 5
x-ratelimit-remaining: 4
x-ratelimit-reset: 1767472567
```
