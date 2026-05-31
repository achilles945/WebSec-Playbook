# Web Application Pentesting Checklist

| Test ID | Test Name | Status | Notes |
| --- | --- | --- | --- |
| **WEB-RECON** | **Reconnaissance & Attack Surface Mapping** |  |  |
| WEB-RECON-01 | Enumerate subdomains |  |  |
| WEB-RECON-02 | Identify development and staging environments |  |  |
| WEB-RECON-03 | Perform directory and file fuzzing |  |  |
| WEB-RECON-04 | Check for directory listing |  |  |
| WEB-RECON-05 | Identify hidden files and backup files |  |  |
| WEB-RECON-06 | Review robots.txt and sitemap.xml |  |  |
| WEB-RECON-07 | Fingerprint technologies and frameworks |  |  |
| WEB-RECON-08 | Analyze JavaScript files |  |  |
| WEB-RECON-09 | Extract endpoints and parameters from JavaScript |  |  |
| WEB-RECON-10 | Discover Swagger/OpenAPI documentation |  |  |
| **WEB-CONF** | **Configuration Testing** |  |  |
| WEB-CONF-01 | Test supported HTTP methods |  |  |
| WEB-CONF-02 | Review security headers |  |  |
| WEB-CONF-03 | Test CORS configuration |  |  |
| WEB-CONF-04 | Check TLS/HTTPS configuration |  |  |
| WEB-CONF-05 | Check for exposed admin panels |  |  |
| WEB-CONF-06 | Check for exposed debug functionality |  |  |
| WEB-CONF-07 | Check for exposed backups |  |  |
| WEB-CONF-08 | Check for exposed source code repositories |  |  |
| WEB-CONF-09 | Check for default credentials |  |  |
| WEB-CONF-10 | Check for verbose error messages |  |  |
| WEB-CONF-11 | Check for cloud storage exposure |  |  |
| WEB-CONF-12 | Test for subdomain takeover opportunities |  |  |
| **WEB-AUTH** | **Authentication Testing** |  |  |
| WEB-AUTH-01 | Test user registration |  |  |
| WEB-AUTH-02 | Test account enumeration |  |  |
| WEB-AUTH-03 | Test login functionality |  |  |
| WEB-AUTH-04 | Test password policy |  |  |
| WEB-AUTH-05 | Test password reset functionality |  |  |
| WEB-AUTH-06 | Test password change functionality |  |  |
| WEB-AUTH-07 | Test remember-me functionality |  |  |
| WEB-AUTH-08 | Test MFA implementation |  |  |
| WEB-AUTH-09 | Test OAuth authentication |  |  |
| WEB-AUTH-10 | Test SSO implementation |  |  |
| **WEB-SESS** | **Session Management Testing** |  |  |
| WEB-SESS-01 | Review session identifiers |  |  |
| WEB-SESS-02 | Review cookie security attributes |  |  |
| WEB-SESS-03 | Test session fixation |  |  |
| WEB-SESS-04 | Test session invalidation after logout |  |  |
| WEB-SESS-05 | Test session timeout |  |  |
| WEB-SESS-06 | Test concurrent sessions |  |  |
| WEB-SESS-07 | Test session reuse |  |  |
| WEB-SESS-08 | Test JWT implementation |  |  |
| WEB-SESS-09 | Test CSRF protections |  |  |
| **WEB-AUTHZ** | **Authorization Testing** |  |  |
| WEB-AUTHZ-01 | Test horizontal privilege escalation |  |  |
| WEB-AUTHZ-02 | Test vertical privilege escalation |  |  |
| WEB-AUTHZ-03 | Test IDOR vulnerabilities |  |  |
| WEB-AUTHZ-04 | Test forced browsing |  |  |
| WEB-AUTHZ-05 | Test function-level authorization |  |  |
| WEB-AUTHZ-06 | Test object-level authorization |  |  |
| WEB-AUTHZ-07 | Test role manipulation |  |  |
| WEB-AUTHZ-08 | Test authorization on API endpoints |  |  |
| **WEB-INPUT** | **Input Validation Testing** |  |  |
| WEB-INPUT-01 | Test reflected XSS |  |  |
| WEB-INPUT-02 | Test stored XSS |  |  |
| WEB-INPUT-03 | Test DOM XSS |  |  |
| WEB-INPUT-04 | Test SQL injection |  |  |
| WEB-INPUT-05 | Test NoSQL injection |  |  |
| WEB-INPUT-06 | Test SSTI |  |  |
| WEB-INPUT-07 | Test command injection |  |  |
| WEB-INPUT-08 | Test XXE |  |  |
| WEB-INPUT-09 | Test SSRF |  |  |
| WEB-INPUT-10 | Test path traversal |  |  |
| WEB-INPUT-11 | Test file inclusion vulnerabilities |  |  |
| WEB-INPUT-12 | Test host header injection |  |  |
| WEB-INPUT-13 | Test HTTP parameter pollution |  |  |
| WEB-INPUT-14 | Test HTTP request smuggling |  |  |
| WEB-INPUT-15 | Test web cache poisoning |  |  |
| WEB-INPUT-16 | Test web cache deception |  |  |
| WEB-INPUT-17 | Test insecure deserialization |  |  |
| WEB-INPUT-18 | Test file upload functionality |  |  |
| **WEB-BUSLOGIC** | **Business Logic Testing** |  |  |
| WEB-BUSLOGIC-01 | Test workflow bypasses |  |  |
| WEB-BUSLOGIC-02 | Test step skipping |  |  |
| WEB-BUSLOGIC-03 | Test state manipulation |  |  |
| WEB-BUSLOGIC-04 | Test race conditions |  |  |
| WEB-BUSLOGIC-05 | Test rate limiting controls |  |  |
| WEB-BUSLOGIC-06 | Test payment workflows |  |  |
| WEB-BUSLOGIC-07 | Test coupon and discount functionality |  |  |
| WEB-BUSLOGIC-08 | Test referral systems |  |  |
| WEB-BUSLOGIC-09 | Test transaction workflows |  |  |
| WEB-BUSLOGIC-10 | Test integrity checks |  |  |
| **WEB-CLIENT** | **Client-Side Testing** |  |  |
| WEB-CLIENT-01 | Test DOM-based vulnerabilities |  |  |
| WEB-CLIENT-02 | Test open redirects |  |  |
| WEB-CLIENT-03 | Test clickjacking protections |  |  |
| WEB-CLIENT-04 | Test browser storage |  |  |
| WEB-CLIENT-05 | Test web messaging |  |  |
| WEB-CLIENT-06 | Test prototype pollution |  |  |
| WEB-CLIENT-07 | Test WebSocket security |  |  |
| WEB-CLIENT-08 | Test client-side resource manipulation |  |  |
| WEB-CLIENT-09 | Test CORS implementation |  |  |
| WEB-CLIENT-10 | Test XSSI vulnerabilities |  |  |