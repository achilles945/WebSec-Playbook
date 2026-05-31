# Recon Methodology

---

# Recon Workflow

```text
Target Discovery
      ↓
Subdomain Enumeration
      ↓
Content Discovery
      ↓
Technology Fingerprinting
      ↓
JavaScript Analysis
      ↓
Parameter Discovery
      ↓
Application Mapping
      ↓
API Discovery
      ↓
Attack Surface Prioritization
```

---

# 0. Target Discovery

## Identify

* Primary Domain
* Subdomains
* IP Addresses
* ASN Assets
* Cloud Assets

## Classify

* Production
* Development
* Staging
* Testing
* Internal

---

# 1. Subdomain Enumeration

## Enumerate

### Certificate Transparency

```text
crt.sh
Censys
```

### Passive Sources

```text
Subfinder
Assetfinder
Amass
```

### DNS Enumeration

```text
Bruteforce
Permutations
Alterations
```

---

## Identify

### Administrative Assets

```text
admin
portal
manage
dashboard
```

### Development Assets

```text
dev
development
test
qa
```

### Staging Assets

```text
stage
staging
beta
preprod
```

### API Assets

```text
api
mobile-api
graphql
```

### Internal Assets

```text
internal
corp
staff
vpn
```

---

## Prioritize

```text
admin.*
api.*
dev.*
stage.*
internal.*
```

---

# 2. Content Discovery

## Directory Discovery

Identify:

```text
/admin
/manage
/internal
/private
/uploads
/api
```

---

## File Discovery

Identify:

```text
.env
config.php
database.sql
backup.zip
```

---

## Backup Discovery

Identify:

```text
.bak
.old
.backup
.zip
.tar
.tar.gz
```

---

## Sensitive Files

Identify:

```text
Logs
Configurations
Credentials
Source Code
Database Dumps
```

---

## Directory Listing

Identify:

```text
Index Of
Directory Contents
File Listings
```

---

# 3. Technology Fingerprinting

## Web Server

Identify:

```text
Apache
Nginx
IIS
LiteSpeed
```

---

## Backend Framework

Identify:

```text
Laravel
Django
Flask
Spring
ASP.NET
Express
```

---

## Frontend Framework

Identify:

```text
React
Angular
Vue
Next.js
```

---

## Infrastructure

Identify:

```text
Cloudflare
AWS
Azure
GCP
Fastly
Akamai
```

---

## APIs

Identify:

```text
REST
GraphQL
WebSockets
gRPC
```

---

# 4. JavaScript Analysis

## Extract Endpoints

Examples:

```text
/api/users
/api/admin
/internal/report
```

---

## Extract Parameters

Examples:

```text
userId
accountId
role
admin
```

---

## Extract API Routes

Examples:

```text
/api/v1
/api/v2
/graphql
```

---

## Extract Hidden Functionality

Identify:

```text
Admin Features
Debug Features
Disabled Features
Internal Features
```

---

## Extract Secrets

Identify:

```text
API Keys
Tokens
JWTs
Credentials
```

---

## Extract URLs

Identify:

```text
Internal URLs
Third-Party URLs
Cloud URLs
```

---

## Search For

```javascript
fetch(
axios(
XMLHttpRequest(
WebSocket(
graphql
token
key
secret
admin
internal
```

---

# 5. Parameter Discovery

## Identify

### URL Parameters

```text
?id=1
?user=admin
?account=123
```

---

### POST Parameters

```text
username
email
password
role
```

---

### JSON Parameters

```json
{
  "userId":1
}
```

---

### Headers

```http
Host
X-Forwarded-For
X-Original-URL
X-Rewrite-URL
```

---

### Cookies

```text
session
token
role
user
```

---

## Test For

```text
Hidden Parameters
Unused Parameters
Undocumented Parameters
Parameter Pollution
Mass Assignment Candidates
```

---

## Prioritize

```text
id
user
userId
account
accountId
role
admin
redirect
url
file
path
```

---

# 6. Application Mapping

## Authentication

Map:

```text
Registration
Login
Password Reset
Password Change
MFA
OAuth
SSO
```

---

## Authorization

Map:

```text
User Roles
Admin Roles
Restricted Features
```

---

## File Handling

Map:

```text
Uploads
Downloads
Imports
Exports
```

---

## Financial Features

Map:

```text
Payments
Transactions
Subscriptions
Coupons
Referrals
```

---

## Administrative Features

Map:

```text
User Management
Role Management
Reporting
System Settings
```

---

# 7. API Discovery

## REST APIs

Identify:

```text
/api
/v1
/v2
/rest
```

---

## GraphQL

Identify:

```text
/graphql
/graphiql
```

Check:

```text
Introspection
Queries
Mutations
Subscriptions
```

---

## WebSockets

Identify:

```text
ws://
wss://
```

Map:

```text
Events
Actions
Messages
```

---

## API Documentation

Identify:

```text
Swagger
OpenAPI
Postman Collections
API Docs
```

---

# 8. Attack Surface Prioritization

## Critical

```text
Authentication
Authorization
Admin Functionality
File Uploads
Sensitive Data
```

---

## High

```text
API Endpoints
State-Changing Actions
Payment Functionality
Internal Features
```

---

## Medium

```text
Search
Reporting
User Preferences
```

---

# Recon Deliverables

Identify:

* Hosts
* Subdomains
* Directories
* Files
* Technologies
* Endpoints
* Parameters
* APIs
* Authentication Mechanisms
* Authorization Boundaries
* Administrative Functionality
* Upload Functionality
* Financial Functionality
* High-Value Targets

---

# Transition

```text
Recon
      ↓
Configuration Testing
      ↓
Authentication Testing
      ↓
Authorization Testing
      ↓
Input Validation Testing
      ↓
Business Logic Testing
```
