# JWT Attacks

JSON Web Tokens (JWTs) are a standardized format used to securely transmit JSON data between systems. JWTs are commonly used for:

* Authentication
* Session management
* Access control

JWT vulnerabilities occur when applications improperly validate, trust, or process JWTs.

JWT attacks can lead to:

* Authentication bypass
* Privilege escalation
* Account takeover
* Access control bypass

JWTs are commonly found in:

* Cookies
* Authorization headers
* API authentication mechanisms

Example JWT:

```text
HEADER.PAYLOAD.SIGNATURE
```

---

## JWT Structure

JWTs consist of three Base64URL-encoded parts separated by dots (`.`).

### Header

Contains token metadata and signing algorithm.

Example:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload

Contains claims about the user.

Example:

```json
{
  "sub": "administrator",
  "role": "admin"
}
```

### Signature

Used to verify token integrity.

---

## Detection

Detect JWT usage in:

* Cookies
* `Authorization: Bearer`
* API requests
* Authentication flows

JWTs usually start with:

```text
eyJ
```

### Common JWT Claims

| Claim  | Meaning        |
| ------ | -------------- |
| `sub`  | Subject / user |
| `role` | User role      |
| `iss`  | Issuer         |
| `exp`  | Expiration     |
| `iat`  | Issued at      |

### Notes

* JWT payloads are only Base64 encoded, not encrypted.
* Sensitive information should not be trusted blindly.
* Modifying claims may expose authorization flaws.

---

## JWT None Algorithm Attack

Occurs when the server accepts unsigned JWTs.

Original Header:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

Modified Header:

```json
{
  "alg": "none",
  "typ": "JWT"
}
```

Modified Payload:

```json
{
  "sub": "administrator"
}
```

Remove the signature completely.

### Notes

* Works when signature verification is disabled.
* Can lead to authentication bypass.
* Common in misconfigured JWT libraries.

---

## JWT Signature Not Verified

Some applications fail to verify JWT signatures properly.

### Attack Method

* Modify JWT payload
* Change claims:

  * `sub`
  * `role`
  * `userid`
* Re-send token without valid signature

Example:

```json
{
  "sub": "administrator",
  "role": "admin"
}
```

### Notes

* If token changes are accepted → signature verification issue exists.
* Common during custom JWT implementation.

---

## JWT Algorithm Confusion Attack

Occurs when applications incorrectly handle symmetric and asymmetric algorithms.

The server expects:

```text
RS256
```

Attacker changes:

```text
HS256
```

The public key is then used as HMAC secret.

### Attack Flow

1. Obtain server public key
2. Change `alg` to `HS256`
3. Modify JWT claims
4. Sign token using public key as HMAC secret

### Notes

* Also called Key Confusion attack.
* Common in poorly implemented JWT libraries.
* Public keys may be exposed through:

  * `/jwks.json`
  * `/.well-known/jwks.json`


---

## JWT Header Parameter Injections

Some applications trust JWT header parameters insecurely.

Common vulnerable headers:

* `jku`
* `jwk`
* `kid`

---

## JKU Header Injection

The `jku` header specifies a URL where the server fetches signing keys.

Example:

```json
{
  "alg": "RS256",
  "jku": "http://attacker.com/jwks.json"
}
```

### Attack Flow

1. Generate attacker-controlled key pair
2. Host malicious JWK Set
3. Modify JWT payload
4. Set `jku` to attacker server
5. Sign token using attacker private key

### Notes

* Works when server trusts arbitrary `jku` URLs.
* Can lead to full authentication bypass.


---

## JWK Header Injection

Some applications allow embedded public keys using the `jwk` header.

Example:

```json
{
  "alg": "RS256",
  "jwk": {
    "kty": "RSA"
  }
}
```

### Attack Flow

1. Generate attacker-controlled key pair
2. Embed public key in JWT header
3. Sign token using attacker private key
4. Send forged JWT

### Notes

* Occurs when applications trust user-supplied keys.
* Can lead to authentication bypass.


---

## KID Header Injection

The `kid` header identifies the signing key.

Some applications use `kid` insecurely to load files from disk.

Example:

```json
{
  "alg": "HS256",
  "kid": "../../../../../../../dev/null"
}
```

### Attack Flow

1. Modify `kid`
2. Use path traversal payloads
3. Sign token with null/known secret
4. Forge valid JWT

### Notes

* Often leads to path traversal.
* `/dev/null` commonly used in labs/testing.
* Common in custom JWT implementations.


---

## Weak JWT Secret

Occurs when JWTs use weak HMAC secrets.

### Detection

Attempt cracking JWT secret using:

* `hashcat`
* `jwt-tool`
* `john`

Common secrets:

```text
secret
password
jwtsecret
admin
```

### Notes

* Weak secrets allow attackers to forge valid JWTs.
* HS256 commonly affected.

---

## JWT Information Disclosure

JWT payloads are only encoded, not encrypted.

### Check for:

* Email addresses
* Internal IDs
* Roles
* API keys
* Sensitive application data

### Notes

* Anyone possessing the JWT can decode it.
* Sensitive data should not be stored in payloads.

---

## JWT Expiration Issues

Applications may use:

* Missing expiration
* Long-lived tokens
* Improper expiration validation

### Check:

* `exp`
* `iat`
* `nbf`

### Notes

* Long-lived JWTs increase account takeover impact.
* Expired tokens should be rejected consistently.

---

## JWT Replay Attacks

Occurs when JWTs remain valid after logout or reuse.

### Check:

* Logout invalidation
* Token reuse across devices
* Token reuse after password change

### Notes

* Stateless JWT implementations commonly affected.
* Stolen tokens may remain valid indefinitely.

---

## Tools

### Burp Suite Professional

* JWT Editor
* JWT Scanner
* Repeater
* Decoder

### Command Line Tools

```bash
jwt-tool
hashcat
john
```

### Notes

* JWT Editor extension is extremely useful during testing.
* Burp can automatically decode JWTs.


---

## Prevention

* Enforce strict signature verification
* Reject `alg:none`
* Validate algorithms explicitly
* Do not trust user-controlled headers
* Use strong secrets
* Implement short expiration times
* Rotate signing keys
* Invalidate tokens after logout
