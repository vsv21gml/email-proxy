# email-proxy

### description
This Amazon SES account will be used to send transactional OTP emails
for user login authentication.

When a user logs into a specific internal system, a one-time password (OTP)
is generated and sent via email.

Multiple affiliated companies share the same corporate email accounts.
To logically separate email flows by affiliate, dedicated domains are
registered in Amazon Route 53 for each affiliate and verified in Amazon SES.

Emails are initially received via these affiliate-specific domains and
then processed by AWS Lambda, which securely forwards the OTP message
to the corresponding internal corporate email address.

All emails are transactional authentication messages only.
No marketing or promotional emails are sent.
All recipients expect to receive these emails as part of system login.

### Email Authentication Architecture
```
User
 |
 | Login request
 v
+---------------------------+
| Internal Authentication   |
| System                    |
+---------------------------+
 |
 | Generate OTP
 v
+---------------------------+
| AWS Lambda (OTP Sender)   |
+---------------------------+
 |
 | Send OTP email
 | From: no-reply@affiliate-domain.example
 v
+---------------------------+
| Amazon SES                |
| (Affiliate-specific       |
|  verified domain)         |
+---------------------------+
 |
 | Receive email via SES
 v
+---------------------------+
| AWS Lambda (Email Proxy)  |
| - Parse OTP               |
| - Validate source domain  |
| - Forward securely        |
+---------------------------+
 |
 | Forward OTP email
 v
+---------------------------+
| Corporate Email System    |
| user@samsung.com          |
+---------------------------+
From: no-reply@samsungsdscoe.com
```
