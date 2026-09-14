# OWASP Juice Shop Security Assessment

![Project](https://img.shields.io/badge/Project-Web%20Application%20Security-0A66C2?style=for-the-badge)
![Assessment](https://img.shields.io/badge/Assessment-Black--Box-red?style=for-the-badge)
![Methodology](https://img.shields.io/badge/Methodology-OWASP%20WSTG-000000?style=for-the-badge&logo=owasp&logoColor=white)
![Purpose](https://img.shields.io/badge/Purpose-Education%20%26%20Defense-success?style=for-the-badge)

## Project Overview

This repository documents a black-box security assessment of **OWASP Juice Shop**, an intentionally vulnerable web application designed for security education and testing. The assessment was completed as a research capstone project for the **International Cyber Security and Digital Forensic Academy (ICDFA)**.

The engagement simulated the perspective of an external threat actor without access to the application's source code or underlying infrastructure. It combined reconnaissance, automated scanning, manual testing, controlled vulnerability validation, risk analysis, and remediation planning.

The project demonstrates practical capabilities in web application security testing, vulnerability analysis, evidence collection, risk-based reporting, and the communication of technical findings to both technical and non-technical audiences.

> **Important:** OWASP Juice Shop is intentionally vulnerable. All activities documented in this repository were conducted for authorized educational and defensive purposes.

---

## Assessment Information

- **Prepared by: Udam Akume Gabriel**
- **Assessment period:** 23 July 2025 to 1 August 2025
- **Report date:** 6 August 2025
- **Assessment type:** External black-box web application assessment  
- **Supervisor: Mr. Aminu Idris, AMCPN** 
- **Target:** OWASP Juice Shop training application
- **Project type:** ICDFA research capstone

---

## Objectives

The assessment was designed to:

- Map the application's externally accessible attack surface.
- Identify weaknesses in authentication, authorization, input handling, session management, data protection, and business logic.
- Validate suspected vulnerabilities through controlled testing.
- Compare automated results with manual analysis.
- Evaluate technical and potential business impact.
- Prioritize findings using a risk-based approach.
- Recommend practical remediation and long-term security improvements.
- Produce clear executive and technical documentation.

---

## Scope

### In Scope

- User registration and authentication
- Product search and browsing
- User account and profile functionality
- Shopping basket and checkout workflows
- Coupon and promotion handling
- Accessible administrative functionality
- Client-side storage and session behavior
- Input validation and output handling
- Object-level and role-based authorization
- Error handling and information disclosure
- Application-layer reconnaissance

### Out of Scope

- Hosting-provider infrastructure
- Operating-system and network-layer exploitation
- Third-party services not directly exposed by the application
- Source-code review
- Social engineering
- Physical security testing
- Denial-of-service testing
- Testing of unrelated live systems

---

## Methodology

The assessment followed a structured workflow informed by the OWASP Web Security Testing Guide and common penetration-testing practices.

### 1. Reconnaissance

The application was mapped to identify accessible pages, routes, technologies, exposed resources, inputs, services, and user workflows.

### 2. Automated Assessment

Automated tools were used to support discovery, crawling, request analysis, directory enumeration, and baseline vulnerability identification.

### 3. Manual Testing

Manual testing focused on validating automated observations and identifying weaknesses that require application context, including authorization and business-logic flaws.

### 4. Controlled Validation

Potential findings were tested using limited proofs of concept within the intended training environment. The goal was to establish technical impact without performing destructive actions.

### 5. Risk Analysis

Each confirmed weakness was evaluated using likelihood, technical impact, required access, user interaction, and potential business consequences.

### 6. Reporting and Remediation

Findings were documented with a description, affected component, evidence, impact, root cause, risk rating, and recommended corrective action.

---

## Tools Used

| Tool | Primary Use |
|---|---|
| Burp Suite Professional | Proxying, crawling, request inspection, replay, and web security testing |
| OWASP ZAP | Baseline automated application assessment |
| SQLMap | Authorized SQL injection validation |
| Nmap | Service and port discovery |
| DIRB | Directory and resource enumeration |
| Nikto | Web server and configuration checks |
| Wappalyzer | Technology identification |
| Browser Developer Tools | Client-side storage, request, script, and DOM analysis |
| John the Ripper | Password-hash strength testing in the lab |
| nslookup and traceroute | Network and DNS reconnaissance |

> Automated output was treated as an input to further analysis, not as proof of a vulnerability. Findings were manually reviewed before being documented as confirmed.

---

## Key Findings

The assessment identified weaknesses across input validation, authentication, authorization, session handling, cryptographic practices, error handling, and application business rules.

| Finding | Category | Reported Priority |
|---|---|---:|
| SQL injection in authentication workflow | Injection | Critical |
| Privilege escalation through token and role manipulation | Broken access control | Critical |
| Reflected and DOM-based cross-site scripting | Injection / client-side security | High |
| Weak password storage using MD5 | Cryptographic failure | High |
| Session-token exposure and reuse risk | Session management | High |
| Sensitive-data exposure | Data protection | High |
| Missing or weak password controls | Authentication | Medium |
| Improper error handling and stack-trace disclosure | Security misconfiguration | Medium |
| Checkout and product workflow manipulation | Insecure design / business logic | Medium |
| Object-level authorization weakness | Broken access control / IDOR | High |

Risk ratings in this repository represent the educational assessment context and should not be treated as ratings for a production deployment.

---

## Findings Summary

### SQL Injection

The authentication workflow accepted unsafe input that could alter database-query logic. Testing demonstrated the risk of authentication bypass and unauthorized data access.

**Root cause:** Unsafe handling of user input in database operations.

**Recommended controls:**

- Use prepared statements and parameterized queries.
- Avoid constructing SQL statements through string concatenation.
- Apply server-side type, length, range, and format validation.
- Restrict database accounts according to least privilege.
- Log and monitor unusual database and authentication activity.
- Retest all affected data-access paths after remediation.

### Cross-Site Scripting

Reflected and DOM-based XSS behavior was observed in application search functionality. Unsafe processing of user-controlled values could allow script execution within a user's browser context.

**Root cause:** Inadequate contextual output encoding and unsafe client-side handling of untrusted data.

**Recommended controls:**

- Apply context-aware output encoding.
- Use trusted sanitization libraries for permitted HTML.
- Avoid unsafe DOM sinks and direct HTML insertion.
- Introduce a restrictive Content Security Policy as defense in depth.
- Protect session cookies with appropriate security attributes.

### Broken Access Control and Privilege Escalation

Testing showed that reliance on client-controlled token claims and incomplete server-side role enforcement could allow a lower-privileged account to reach restricted functionality.

**Root cause:** Security decisions were not enforced consistently on the server for every sensitive operation.

**Recommended controls:**

- Verify authorization on every server-side request.
- Validate token signatures, algorithms, issuers, audiences, and expiration.
- Deny access by default.
- Centralize role and permission checks.
- Never trust client-side role indicators as authorization controls.
- Record and alert on suspicious privilege changes and access denials.

### Insecure Direct Object Reference

Object identifiers used in application workflows could be modified to request another user's resources or alter protected transaction data.

**Root cause:** Missing or inconsistent object-level authorization.

**Recommended controls:**

- Confirm ownership and permission before every object operation.
- Scope database queries to the authenticated user where appropriate.
- Use indirect identifiers only as defense in depth, not as authorization.
- Test horizontal and vertical authorization systematically.
- Log unusual object-access patterns.

### Session Management Weaknesses

The assessment examined token storage, token reuse, session invalidation, cookie protections, and session lifecycle behavior. Weak controls could enable unauthorized session reuse if a valid token were exposed.

**Recommended controls:**

- Use high-entropy, short-lived session identifiers.
- Apply `Secure`, `HttpOnly`, and an appropriate `SameSite` policy to cookies.
- Rotate sessions after authentication and privilege changes.
- Enforce idle and absolute expiration.
- Invalidate sessions server-side during logout and credential changes.
- Avoid exposing tokens in URLs or insecure client-side locations.

### Weak Password Storage and Password Policy

The application used MD5 for password hashing and permitted weak password choices in the training context.

**Recommended controls:**

- Store passwords using a modern password-hashing function such as Argon2id, scrypt, or bcrypt.
- Use unique salts and appropriate work factors.
- Block commonly used and compromised passwords.
- Apply rate limiting and monitoring to authentication attempts.
- Support multi-factor authentication for sensitive accounts.
- Avoid arbitrary complexity rules that encourage predictable password patterns.

### Improper Error Handling

Certain invalid requests produced detailed error information that could reveal application structure or implementation details.

**Recommended controls:**

- Return generic error messages to users.
- Store detailed diagnostic information in protected server-side logs.
- Disable debug output in production.
- Remove internal paths, stack traces, and sensitive values from responses.
- Monitor repeated requests that intentionally trigger application errors.

### Business Logic Weaknesses

Testing of basket, product, address, coupon, and checkout workflows identified cases where client-controlled values were not fully validated against server-side business rules.

**Recommended controls:**

- Recalculate prices, quantities, discounts, and transaction state server-side.
- Validate resource ownership during every stage of a workflow.
- Enforce valid state transitions.
- Add abuse cases to automated unit, integration, and acceptance tests.
- Monitor unusual purchasing, coupon, basket, and checkout behavior.

---

## Remediation Roadmap

### Immediate Priority

- Eliminate SQL injection through parameterized database access.
- Enforce server-side authorization for all protected operations.
- Correct token validation and role enforcement.
- Replace weak password hashing.
- Address XSS through contextual encoding and safe DOM handling.
- Remove sensitive information from client-facing errors.

### Short-Term Improvements

- Introduce centralized access-control and input-validation mechanisms.
- Strengthen session lifecycle management.
- Review critical workflows for abuse cases.
- Improve security logging and alerting.
- Add security-focused regression tests.
- Perform a focused secure-code review.

### Long-Term Improvements

- Integrate security throughout the software development lifecycle.
- Establish recurring vulnerability assessments and penetration tests.
- Implement continuous monitoring and incident-response procedures.
- Provide secure-coding education for development teams.
- Maintain an application threat model.
- Track remediation through measurable ownership and deadlines.

---

## Skills Demonstrated

- Black-box web application assessment
- Attack-surface mapping and reconnaissance
- Manual and automated security testing
- HTTP request and response analysis
- Authentication and session-security testing
- Access-control and IDOR assessment
- SQL injection and XSS validation
- Business-logic security testing
- Risk analysis and prioritization
- Evidence collection and sanitization
- Executive and technical reporting
- Remediation planning
- Ethical testing and responsible documentation

---

## Limitations

- The target was an intentionally vulnerable training application.
- The assessment did not include source-code or infrastructure review.
- Findings are specific to the tested environment and assessment period.
- Public evidence is intentionally limited and sanitized.
- This repository is a portfolio case study, not an assurance statement for any production system.

---

## Ethical Use Statement

This repository is intended exclusively for cybersecurity education, authorized testing, defensive research, and secure-development improvement. Do not test applications, accounts, systems, or networks without explicit authorization from their owner.

The repository intentionally emphasizes methodology, risk, and remediation rather than publishing credentials, live tokens, sensitive evidence, or weaponized exploitation material.

---

## Contact

**Udam Akume Gabriel**  
Digital Forensics and Incident Response | Web Application Security

- **Email:** [udamgabrielakume99@gmail.com](mailto:udamgabrielakume99@gmail.com)
- **LinkedIn:** [linkedin.com/in/udam-akume-gabriel-09a8bb334](https://www.linkedin.com/in/udam-akume-gabriel-09a8bb334)

---

## Disclaimer

OWASP Juice Shop and OWASP are referenced solely to identify the authorized training application used in this educational project. This repository does not claim that the findings apply to unrelated systems or current deployments. Product names and trademarks belong to their respective owners.
