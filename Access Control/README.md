# Broken Access Control

Access control determines whether a user is allowed to perform an action or access a resource.

A **broken access control vulnerability** occurs when an application fails to properly enforce these restrictions, allowing users to access resources or perform actions they should not be permitted to.

Access control depends on three components:

- **Authentication** – verifies the identity of a user
- **Session management** – associates requests with a specific user
- **Authorization** – determines what actions the user is allowed to perform

### Impact

- Access sensitive data belonging to other users
- Access administrative functionality
- Modify or delete data without authorization
- Escalate privileges within the application
- Full compromise of application functionality

---

## Detection

Access control vulnerabilities are detected by testing whether restricted resources can be accessed by unauthorized users.

Common indicators:

- Direct access to **admin URLs**
- Parameters controlling **user IDs**
- Hidden role values in **cookies or requests**
- API endpoints that return data without authorization checks

Example:

```
https://target.com/myaccount?id=123
```

Test by modifying the identifier:

```
https://target.com/myaccount?id=124
```

If another user's data is returned, access control is broken.

---

## Access Control Types

### Vertical Access Control

Restricts functionality based on **user roles**.

Example:

- Admin users can delete accounts
- Normal users cannot

If a normal user accesses:

```
https://target.com/admin
```

this is **vertical privilege escalation**.

---

### Horizontal Access Control

Restricts access to resources belonging to a specific user.

Example:

```
https://target.com/myaccount?id=123
```

If changing the ID reveals another user's account:

```
https://target.com/myaccount?id=456
```

this is **horizontal privilege escalation**.

---

### Context-Dependent Access Control

Restricts actions based on application state or workflow.

Example:

Users should not modify orders **after payment**.

If the request can still be sent directly:

```
POST /modifyOrder
```

the access control is broken.

---

## Privilege Escalation

### Vertical Privilege Escalation

A user gains access to functionality reserved for higher-privileged users.

Example:

```
/admin
/admin/deleteUser
```

---

### Horizontal Privilege Escalation

A user accesses another user's resources.

Example:

```
/account?id=123
/account?id=124
```

---

### Horizontal to Vertical Privilege Escalation

An attacker first accesses another user's account and then gains higher privileges.

Example:

```
/account?id=456
```

If user `456` is an administrator, the attacker gains admin access.

---

## Insecure Direct Object References (IDOR)

IDOR is a type of access control vulnerability where an application uses **user-supplied input to access objects directly**.

If the application does not verify ownership of the object, attackers can access unauthorized resources.

Example:

```
https://target.com/customer_account?customer_number=132355
```

Changing the parameter:

```
https://target.com/customer_account?customer_number=132356
```

may expose another user's account.

---

### IDOR with Static Files

Sometimes resources are stored as files on the server.

Example:

```
https://target.com/static/12144.txt
```

If file names are predictable, attackers may access other files:

```
https://target.com/static/12145.txt
```

---

## Common Access Control Weaknesses

### Unprotected Functionality

Sensitive endpoints are accessible without authentication.

Example:

```
https://target.com/admin
```

---

### Parameter-Based Role Control

User roles are stored in request parameters or cookies.

Example:

```
role=user
role=admin
```

Attackers can modify the value.

---

### HTTP Method Bypass

Access control is enforced only for certain HTTP methods.

Example:

```
POST /admin/deleteUser
```

If `GET` works instead:

```
GET /admin/deleteUser
```

access control can be bypassed.

---

### URL Matching Discrepancies

Access control filters may treat URLs differently.

Example bypasses:

```
/admin/deleteUser/
/ADMIN/deleteUser
/admin/deleteUser.anything
```

---

## Multi-Step Process Bypass

Some applications protect only certain steps of a workflow.

Example process:

1. Load form
2. Submit changes
3. Confirm action

If step 3 lacks authorization checks, attackers can directly send the request.

---

## Referer-Based Access Control

Some applications rely on the `Referer` header for authorization.

Example requirement:

```
Referer: /admin
```

Attackers can modify the header manually to bypass the control.

---

## Location-Based Access Control

Access may be restricted based on geographic location.

Example restrictions:

- Country-based access
- IP-based restrictions

Attackers may bypass this using **VPNs or proxies**.