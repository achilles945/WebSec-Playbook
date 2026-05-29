# OAuth Authentication

## Overview

OAuth (Open Authorization) is an authorization framework that allows one application to access resources on behalf of a user without requiring the user's credentials.

OAuth was designed for:

```text
Authorization
```

not:

```text
Authentication
```

However, modern applications commonly use OAuth as an authentication mechanism through OpenID Connect (OIDC).

This is where many security issues originate.

When implemented incorrectly, OAuth vulnerabilities can lead to:

```text
Account Takeover
Authentication Bypass
Privilege Escalation
Sensitive Data Disclosure
```

---

## Why OAuth Exists

Before OAuth existed, third-party applications often required users to provide their credentials directly.

Example:

```text
Calendar App
      ↓
Requests Google Password
      ↓
Stores Google Password
```

Problems:

```text
Password Exposure
Unlimited Access
No Granular Permissions
No Revocation
```

OAuth solves this by introducing delegated access.

Instead of giving credentials:

```text
User
    ↓
Authorization Server
    ↓
Issues Token
    ↓
Client Uses Token
```

The client never receives the user's password.

---

## OAuth Security Model

OAuth consists of four primary components.

Understanding their trust relationships is critical.

Most OAuth vulnerabilities occur because one component incorrectly trusts another.

---

### Resource Owner

The user.

Example:

```text
Carlos
```

Owns:

```text
Account
Data
Resources
```

---

### Client Application

The application requesting access.

Example:

```text
example.com
```

Requests permission from the user.

Examples:

```text
Login With Google
Login With GitHub
Login With Microsoft
```

---

### Authorization Server

Responsible for:

```text
Authentication
Consent
Token Issuance
```

Examples:

```text
accounts.google.com
github.com
login.microsoftonline.com
```

The Authorization Server verifies:

```text
Who the user is.
```

---

### Resource Server

Stores protected resources.

Examples:

```text
UserInfo API
Google APIs
GitHub APIs
Microsoft Graph
```

The Resource Server verifies:

```text
What the token can access.
```

---

## OAuth Tokens

Understanding tokens is mandatory for OAuth testing.

Every OAuth vulnerability revolves around stealing, manipulating, or abusing tokens.

---

### Authorization Code

Temporary credential.

Example:

```text
ABC123XYZ
```

Purpose:

```text
Exchange For Access Token
```

### Why It Exists

Without authorization codes:

```text
Access Token
      ↓
Returned Through Browser
```

which increases exposure.

Authorization codes allow:

```text
Browser
      ↓
Authorization Code
      ↓
Server
      ↓
Access Token
```

This keeps access tokens away from the browser.

### Pentesting Focus

```text
Code Theft
Code Leakage
Code Reuse
Code Interception
```

---

### Access Token

Primary authorization credential.

Example:

```text
Bearer eyJhbGci...
```

Used for:

```text
Accessing APIs
Accessing Resources
Performing Actions
```

### Attacker Goal

Steal token.

Why?

Because possession often equals authorization.

Example:

```http
GET /userinfo
Authorization: Bearer TOKEN
```

### Pentesting Focus

```text
Token Theft
Token Leakage
Token Reuse
Scope Abuse
```

---

### Refresh Token

Long-lived credential.

Used to obtain new access tokens.

Flow:

```text
Refresh Token
       ↓
New Access Token
```

### Why It Matters

Refresh tokens often survive:

```text
Session Expiration
Access Token Expiration
```

### Pentesting Focus

```text
Token Theft
Token Storage
Token Rotation
Token Reuse
```

---

### ID Token

Introduced by OpenID Connect.

Usually:

```text
JWT
```

Contains:

```text
Identity Information
Claims
Authentication Data
```

Example:

```json
{
  "sub":"12345",
  "email":"user@example.com"
}
```

### Important Distinction

Access Token proves:

```text
Authorization
```

ID Token proves:

```text
Identity
```

Many applications confuse the two.

This creates authentication vulnerabilities.

---

## OAuth Flows

Different OAuth flows expose different attack surfaces.

Always identify the flow before testing.

---

### Authorization Code Flow

Most common flow.

Most secure flow.

Most frequently encountered.

### Flow

```text
User
   ↓
Authorization Request
   ↓
Authorization Server
   ↓
Authorization Code
   ↓
Client Backend
   ↓
Access Token
```

### Detailed Process

#### Step 1

User clicks:

```text
Login With Google
```

Application redirects:

```http
GET /authorize?
client_id=123
redirect_uri=https://example.com/callback
response_type=code
scope=openid profile
state=random
```

#### Step 2

User authenticates.

Authorization server verifies identity.

#### Step 3

Authorization server issues:

```text
Authorization Code
```

Example:

```http
GET /callback?code=ABC123
```

#### Step 4

Client exchanges:

```text
Authorization Code
```

for:

```text
Access Token
```

using server-to-server communication.

#### Step 5

Client accesses user resources.

### Pentesting Focus

```text
Authorization Code Theft
redirect_uri Validation
State Validation
Code Reuse
Code Leakage
```

---

### Implicit Flow

Older flow.

Less secure.

Access token returned directly to browser.

Example:

```http
/callback#access_token=TOKEN
```

### Why It Is Dangerous

Access token travels through:

```text
Browser
History
Logs
Extensions
JavaScript
```

Increasing attack surface.

### Pentesting Focus

```text
Token Leakage
Token Theft
Client-Side Validation
Authentication Bypass
```

---

## OAuth Recon

### Identifying OAuth

Look for:

```text
Login With Google
Login With GitHub
Login With Microsoft
Login With Facebook
```

---

### Common Endpoints

```text
/oauth
/oauth2
/authorize
/auth
/callback
/oauth/callback
```

---

### Important Parameters

```text
client_id
redirect_uri
response_type
scope
state
nonce
```

These parameters become primary testing targets.

---

### Discovery Endpoints

Check:

```text
/.well-known/oauth-authorization-server
/.well-known/openid-configuration
```

Useful information:

```text
Authorization Endpoint
Token Endpoint
JWKS Endpoint
Registration Endpoint
Supported Flows
Supported Scopes
```

---

## OAuth Threat Modeling

Before testing vulnerabilities, ask:

```text
Can I steal authorization codes?

Can I steal access tokens?

Can I manipulate redirect_uri?

Can I bypass state validation?

Can I abuse scopes?

Can I register a malicious client?

Can I force server-side requests?
```

These questions drive the entire assessment.

---

## OAuth Attack Surface

### Authorization Endpoint

Example:

```http
GET /authorize
```

Primary targets:

```text
state
scope
response_type
redirect_uri
```

---

### Callback Endpoint

Example:

```http
GET /callback
```

Primary targets:

```text
Code Leakage
Token Leakage
Account Linking
```

---

### Token Endpoint

Example:

```http
POST /token
```

Primary targets:

```text
Code Reuse
Grant Validation
Client Validation
```

---

### UserInfo Endpoint

Example:

```http
GET /userinfo
```

Primary targets:

```text
Sensitive Data Exposure
Scope Enforcement
Authorization Issues
```

---

### Registration Endpoint

Primary targets:

```text
Dynamic Registration Abuse
SSRF
Client Manipulation
```

---

## Practical Testing Methodology

### Phase 1 — Identify OAuth

Determine:

```text
OAuth
OIDC
Social Login
```

usage.

---

### Phase 2 — Map Flow

Identify:

```text
Authorization Endpoint
Callback Endpoint
Token Endpoint
UserInfo Endpoint
Discovery Endpoint
```

---

### Phase 3 — Map Parameters

Review:

```text
client_id
redirect_uri
response_type
scope
state
nonce
```

---

### Phase 4 — Identify Flow

Determine:

```text
Authorization Code Flow
Implicit Flow
OIDC Flow
Hybrid Flow
```

---

### Phase 5 — Threat Modeling

Ask:

```text
Can I steal the code?

Can I steal the token?

Can I manipulate redirects?

Can I bypass validation?
```

---

### Phase 6 — Vulnerability Testing

Test:

```text
State Validation
redirect_uri Validation
Code Theft
Token Leakage
Scope Manipulation
Account Linking
```

---

### Phase 7 — Escalation

Attempt:

```text
Authentication Bypass
Account Takeover
Privilege Escalation
Sensitive Data Exposure
```

---

## OAuth Vulnerabilities

---

## Improper Implicit Flow Validation

### Overview

This vulnerability occurs when the client application trusts identity information received from the browser instead of validating it using the OAuth provider.

The application assumes:

```text
Email Address
Username
User ID
```

received from the browser belong to the owner of the access token.

If validation is missing, attackers may authenticate as arbitrary users.

---

### How It Works Internally

Implicit Flow returns the access token directly to the browser.

Example:

```http
GET /callback#access_token=TOKEN
```

The application should:

```text
Validate Access Token
        ↓
Retrieve User Information
        ↓
Create Session
```

Vulnerable applications perform:

```text
Receive Access Token
        ↓
Receive User Information
        ↓
Trust Browser Data
        ↓
Create Session
```

---

### Attack Flow

```text
Attacker Authenticates
        ↓
Obtains Valid Access Token
        ↓
Modifies Email Address
        ↓
Application Trusts Modified Data
        ↓
Victim Account Access
```

---

### Testing Methodology

Intercept authentication requests.

Look for:

```json
{
  "email":"user@example.com",
  "access_token":"..."
}
```

Modify:

```json
{
  "email":"victim@example.com",
  "access_token":"..."
}
```

Observe whether the application:

```text
Creates Session
Returns Victim Data
Links To Victim Account
```

---

### Impact

```text
Authentication Bypass
Account Takeover
Identity Spoofing
```

---

## Missing State Validation

### Overview

The state parameter protects OAuth flows from Cross-Site Request Forgery (CSRF).

Without proper state validation, the application cannot determine whether an OAuth response belongs to the login flow it originally initiated.

This creates OAuth-specific CSRF attacks.

---

### Why State Exists

OAuth authentication involves multiple redirects.

Flow:

```text
Application
       ↓
OAuth Provider
       ↓
Application
```

When the user returns from the provider, the application must determine:

```text
Is this response associated
with a legitimate login request?
```

The state parameter provides this association.

---

### Secure Flow

```text
Login Request
        ↓
Generate Random State
        ↓
Store State
        ↓
Send To Provider
        ↓
State Returned
        ↓
Validate State
```

---

### Vulnerable Flow

```text
Login Request
        ↓
No State Validation
        ↓
Accept Any OAuth Response
```

---

### Attack Flow

```text
Attacker Starts OAuth Login
        ↓
Obtains Authorization Code
        ↓
Victim Visits Attack Link
        ↓
Application Accepts Code
        ↓
Victim Account Linked To Attacker OAuth Account
```

---

### Testing Methodology

Inspect authorization request.

Check for:

```text
state=
```

Determine whether:

```text
State Missing
State Predictable
State Reusable
State Not Validated
```

Attempt:

```text
Remove State
Modify State
Replay State
```

---

### Impact

```text
OAuth CSRF
Forced Account Linking
Account Takeover
```

---

## Authorization Code Theft

### Overview

Authorization codes are temporary credentials used to obtain access tokens.

Anyone possessing a valid authorization code may potentially obtain the associated access token.

---

### Why Authorization Codes Matter

Authorization Code Flow exists specifically to prevent access tokens from being exposed through the browser.

Flow:

```text
Authorization Code
       ↓
Backend Exchange
       ↓
Access Token
```

If the code is stolen before exchange:

```text
Attacker Obtains Token
```

---

### Common Causes

```text
Weak redirect_uri Validation
Open Redirects
Code Leakage
Proxy Pages
Callback Misconfiguration
```

---

### Attack Flow

```text
Victim Authenticates
        ↓
Provider Issues Code
        ↓
Code Redirected To Attacker
        ↓
Attacker Exchanges Code
        ↓
Account Takeover
```

---

### Testing Methodology

Focus on:

```text
redirect_uri
```

Try:

```text
Alternative Paths
Alternative Subdomains
Open Redirects
Path Traversal Sequences
URL Encoding
```

Determine whether authorization codes can be redirected away from trusted endpoints.

---

### Impact

```text
Authorization Code Theft
Access Token Theft
Account Takeover
```

---

## Flawed redirect_uri Validation

### Overview

OAuth providers must verify that authorization responses are only sent to trusted locations.

Weak validation allows attackers to receive:

```text
Authorization Codes
Access Tokens
ID Tokens
```

---

### Common Validation Mistakes

#### Prefix Validation

Provider checks:

```text
Starts With
```

Example:

```text
https://trusted.com
```

Bypass:

```text
https://trusted.com.attacker.com
```

---

#### Substring Validation

Provider checks:

```text
Contains trusted.com
```

Bypass:

```text
https://attacker.com/?trusted.com
```

---

#### Wildcard Validation

Provider allows:

```text
*.trusted.com
```

This may expose:

```text
Forgotten Subdomains
Subdomain Takeovers
Development Systems
```

---

#### Duplicate Parameters

Example:

```text
redirect_uri=A
redirect_uri=B
```

Different parsers may process different values.

---

### Testing Methodology

Test:

```text
Subdomains
Alternative Hosts
Userinfo Syntax
Encoded Payloads
Duplicate Parameters
Open Redirect Chains
```

Examples:

```text
https://trusted.com.attacker.com
```

```text
https://trusted.com@attacker.com
```

```text
https://attacker.com/?trusted.com
```

---

### Impact

```text
Authorization Code Theft
Token Theft
Account Takeover
```

---

## OAuth Token Leakage

### Overview

OAuth tokens frequently leak through unintended locations.

Unlike passwords, access tokens often provide immediate access to protected resources.

---

### Common Sources

```text
URLs
Browser History
Logs
Analytics Platforms
Referrer Headers
Debug Output
```

---

### Implicit Flow Exposure

Example:

```http
/callback#access_token=TOKEN
```

Tokens may be exposed to:

```text
JavaScript
Browser Extensions
Client-Side Logging
```

---

### Proxy Page Exposure

Attackers may exploit:

```text
Open Redirects
XSS
HTML Injection
PostMessage Issues
```

to extract tokens after successful OAuth authentication.

---

### Testing Methodology

Search for tokens inside:

```text
URLs
Responses
Logs
Referrer Headers
Local Storage
Session Storage
```

Observe:

```text
Third-Party Requests
Analytics Requests
Redirect Chains
```

---

### Impact

```text
Account Takeover
Sensitive Data Disclosure
Unauthorized Access
```

---

## Scope Manipulation

### Overview

Scopes define what information and functionality an OAuth client may access.

Examples:

```text
profile
email
address
phone
```

Applications frequently assume scopes cannot be manipulated.

---

### How Scopes Work

Client requests:

```text
profile email
```

Provider approves:

```text
profile email
```

Access token receives:

```text
profile email
```

permissions.

---

### Vulnerable Scenarios

```text
Scope Validation Missing
Scope Validation Inconsistent
Provider Misconfiguration
Application Trust Issues
```

---

### Testing Methodology

Attempt:

```text
Additional Scopes
Hidden Scopes
Undocumented Scopes
Admin Scopes
```

Observe:

```text
Additional Data
Additional Endpoints
Additional Functionality
```

---

### Impact

```text
Sensitive Data Disclosure
Privilege Escalation
Unauthorized Access
```

---

## Unverified User Registration

### Overview

Some OAuth providers allow users to register accounts without verifying ownership of identity attributes.

Example:

```text
Email Address
```

may not be verified.

Applications often trust identity information received from the provider.

---

### Attack Flow

```text
Attacker Registers Account
        ↓
Uses Victim Email Address
        ↓
OAuth Provider Does Not Verify Ownership
        ↓
Application Trusts Provider Data
        ↓
Victim Account Access
```

---

### Testing Methodology

Determine:

```text
Does Provider Verify Email?
Does Application Require Verification?
Does Application Trust Claims Automatically?
```

Test:

```text
Email Changes
Unverified Emails
Secondary Emails
Alias Addresses
```

---

### Impact

```text
Account Takeover
Identity Spoofing
Authentication Bypass
```

---

## OpenID Connect (OIDC)

---

## Overview

OpenID Connect (OIDC) is an identity layer built on top of OAuth 2.0.

OAuth provides:

```text
Authorization
```

OIDC provides:

```text
Authentication
```

OAuth answers:

```text
What can this application access?
```

OIDC answers:

```text
Who is this user?
```

---

## Why OIDC Exists

OAuth was never designed to identify users.

OAuth only allows applications to obtain access tokens.

Applications started using OAuth for authentication.

This introduced ambiguity:

```text
Who authenticated the user?

How can identity be verified?

How can authentication claims be trusted?
```

OIDC solves these problems by introducing:

```text
ID Tokens
Identity Claims
Authentication Metadata
```

---

## OIDC Components

OIDC introduces several additional components.

---

### ID Token

Primary authentication artifact.

Usually:

```text
JWT
```

Contains identity information.

Example:

```json
{
  "sub":"123456",
  "email":"user@example.com",
  "name":"Carlos"
}
```

---

### Claims

Claims are identity attributes contained inside the ID token.

Examples:

```text
sub
email
name
preferred_username
phone_number
```

Applications frequently trust these claims.

Incorrect validation leads to authentication vulnerabilities.

---

### Nonce

Random value generated by the client.

Purpose:

```text
Replay Protection
Request Correlation
```

Flow:

```text
Client Generates Nonce
        ↓
Provider Stores Nonce
        ↓
Nonce Included In ID Token
        ↓
Client Validates Nonce
```

---

## OIDC Discovery

Many OIDC implementations expose:

```text
/.well-known/openid-configuration
```

This endpoint is extremely valuable during reconnaissance.

---

### Information Disclosure

Discovery endpoints commonly reveal:

```text
Authorization Endpoint
Token Endpoint
JWKS Endpoint
Registration Endpoint
Supported Scopes
Supported Response Types
Supported Grant Types
```

---

### Testing Methodology

Always retrieve:

```text
/.well-known/openid-configuration
```

Identify:

```text
Supported Features
Advanced Functionality
Attack Surface
```

---

## JWKS

OIDC commonly publishes:

```text
jwks_uri
```

Example:

```text
https://provider.com/.well-known/jwks.json
```

Contains:

```text
Public Keys
Key IDs
Signing Metadata
```

Applications use these keys to validate ID tokens.

---

## Vulnerability — Improper ID Token Validation

### Overview

One of the most common OIDC vulnerabilities.

Application accepts ID tokens without performing proper validation.

---

### Why Validation Matters

An ID token is only trustworthy if:

```text
Signature Valid
Issuer Valid
Audience Valid
Expiration Valid
Nonce Valid
```

Without validation:

```text
Identity Cannot Be Trusted
```

---

### Required Validation Checks

#### Signature Validation

Verify:

```text
Token Signed By Trusted Provider
```

Without signature validation:

```text
Attacker Creates Arbitrary Token
```

---

#### Issuer Validation

Verify:

```text
iss
```

matches trusted provider.

Example:

```json
{
  "iss":"https://accounts.google.com"
}
```

Without validation:

```text
Untrusted Provider Accepted
```

---

#### Audience Validation

Verify:

```text
aud
```

matches the client application.

Example:

```json
{
  "aud":"client123"
}
```

Without validation:

```text
Token Intended For Another Application Accepted
```

---

#### Expiration Validation

Verify:

```text
exp
```

Without validation:

```text
Expired Tokens Remain Valid
```

---

#### Nonce Validation

Verify:

```text
nonce
```

matches original request.

Without validation:

```text
Replay Attacks Become Possible
```

---

### Impact

```text
Authentication Bypass
Account Takeover
Identity Spoofing
```

---

## Vulnerability — Missing Nonce Validation

### Overview

Nonce protects OIDC authentication flows from replay attacks.

Without nonce validation:

```text
Old Authentication Responses
Can Be Reused
```

---

### Secure Flow

```text
Generate Nonce
       ↓
Store Nonce
       ↓
Send To Provider
       ↓
Receive ID Token
       ↓
Validate Nonce
```

---

### Vulnerable Flow

```text
Generate Nonce
       ↓
Receive ID Token
       ↓
No Validation
```

---

### Testing Methodology

Identify:

```text
nonce=
```

inside authentication requests.

Determine:

```text
Nonce Missing
Nonce Reusable
Nonce Predictable
Nonce Not Validated
```

Attempt:

```text
Replay Authentication Responses
Modify Nonce
Remove Nonce
```

---

### Impact

```text
Authentication Bypass
Replay Attacks
Account Takeover
```

---

## Vulnerability — Dynamic Client Registration Abuse

### Overview

Some providers allow applications to register themselves dynamically.

Discovery endpoint often exposes:

```text
registration_endpoint
```

---

### Why It Matters

Registration parameters frequently cause server-side requests.

Examples:

```text
logo_uri
jwks_uri
sector_identifier_uri
request_uris
```

---

### Attack Surface

Provider performs:

```text
Provider
      ↓
Fetch Attacker URL
      ↓
Process Response
```

This creates SSRF opportunities.

---

### Testing Methodology

Locate:

```text
registration_endpoint
```

Register a client.

Inspect supported fields.

Focus on:

```text
logo_uri
jwks_uri
sector_identifier_uri
request_uris
```

Attempt:

```text
External URLs
Internal URLs
Cloud Metadata URLs
```

Observe outbound requests.

---

### Impact

```text
SSRF
Internal Network Access
Cloud Metadata Exposure
```

---

## Vulnerability — request_uri SSRF

### Overview

OIDC supports:

```text
request_uri
```

parameter.

Instead of sending the request directly:

```text
Client
      ↓
Provides URL
      ↓
Provider Fetches URL
```

---

### Example

```http
GET /authorize?
request_uri=https://attacker.com/request.jwt
```

Provider retrieves:

```text
https://attacker.com/request.jwt
```

and processes its contents.

---

### Why It Matters

If validation is weak:

```text
Attacker Controls URL
```

Provider may fetch:

```text
Internal Systems
Cloud Metadata Services
Local Services
```

---

### Testing Methodology

Check:

```text
request_uri_parameter_supported
```

inside:

```text
/.well-known/openid-configuration
```

Attempt:

```text
request_uri=http://attacker.com/jwt
```

Then test:

```text
127.0.0.1
localhost
Internal IP Ranges
Cloud Metadata Endpoints
```

---

### Impact

```text
SSRF
Internal Network Access
Cloud Metadata Exposure
Credential Theft
```

---

## Advanced OAuth / OIDC Recon

### Discovery Endpoint Enumeration

Always inspect:

```text
/.well-known/openid-configuration
```

Extract:

```text
authorization_endpoint
token_endpoint
userinfo_endpoint
jwks_uri
registration_endpoint
```

---

### Response Type Enumeration

Identify supported:

```text
code
token
id_token
code token
code id_token
```

Different response types expose different attack surfaces.

---

### Scope Enumeration

Enumerate:

```text
openid
profile
email
address
phone
offline_access
```

Look for:

```text
Undocumented Scopes
Admin Scopes
Internal Scopes
```

---

### Grant Type Enumeration

Identify:

```text
authorization_code
implicit
client_credentials
password
refresh_token
device_code
```

Each grant type introduces unique attack paths.

---

## OAuth / OIDC Attack Chaining

### Authorization Code Theft

```text
Weak redirect_uri Validation
            ↓
Authorization Code Theft
            ↓
Access Token Theft
            ↓
Account Takeover
```

---

### Missing State Validation

```text
Missing State
        ↓
OAuth CSRF
        ↓
Forced Account Linking
        ↓
Account Takeover
```

---

### ID Token Validation Failure

```text
Weak JWT Validation
         ↓
Forged ID Token
         ↓
Authentication Bypass
```

---

### Dynamic Registration Abuse

```text
Dynamic Registration
          ↓
SSRF
          ↓
Cloud Metadata Access
          ↓
Credential Theft
```

---

## OAuth / OIDC Pentesting Workflow

### Phase 1 — Identify Technology

Determine:

```text
OAuth
OIDC
Social Login
```

---

### Phase 2 — Enumerate Endpoints

Identify:

```text
Authorization Endpoint
Token Endpoint
UserInfo Endpoint
JWKS Endpoint
Registration Endpoint
```

---

### Phase 3 — Map Trust Relationships

Determine:

```text
Who Issues Tokens

Who Validates Tokens

Who Trusts Claims

Who Creates Sessions
```

---

### Phase 4 — Test OAuth Vulnerabilities

Focus:

```text
State Validation
redirect_uri Validation
Authorization Code Theft
Token Leakage
Scope Manipulation
```

---

### Phase 5 — Test OIDC Vulnerabilities

Focus:

```text
ID Token Validation
Nonce Validation
JWKS Validation
Dynamic Registration
request_uri
```

---

### Phase 6 — Escalation

Attempt:

```text
Authentication Bypass
Account Takeover
Privilege Escalation
Sensitive Data Disclosure
SSRF
Cloud Credential Theft
```

---

