# Verification code for account

#### **Endpoint**

**POST**\
`https://dashboardapi.charidy.com/orgarea/api/v1/login/verify/code`

#### **Description**

Verifies the user's login by validating the email and the verification code sent to the user.

#### **Request Body**

Send a JSON object containing the user's email and the verification code.

```
{ "email": "test@charidy.com", "code": "123456" }
```

#### **Response**

A successful verification returns:\
\
`{`\
`"approved": true,`\
`"result": "ok"`\
`}`<br>

#### **Notes**

* `approved: true` indicates the verification code is valid.
* `result: "ok"` confirms the request was processed successfully.
