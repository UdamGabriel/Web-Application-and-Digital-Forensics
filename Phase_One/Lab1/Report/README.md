# Advanced Web Application Security Labs

![Security](https://img.shields.io/badge/Focus-Web%20Application%20Security-red?style=for-the-badge)
![Testing](https://img.shields.io/badge/Testing-Authorized%20Lab-green?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-blue?style=for-the-badge&logo=kalilinux&logoColor=white)
![Server](https://img.shields.io/badge/Server-XAMPP-orange?style=for-the-badge)

## Overview

This repository documents a series of practical labs completed as part of the **Advanced Web Application Security (WASF1203)** course. The work focused on identifying, validating, understanding, and mitigating common and advanced web application vulnerabilities in a controlled, intentionally vulnerable local environment.

The labs combined manual security testing, automated assessment, source-code review, server-side configuration, database interaction, and remediation. Each exercise was designed to demonstrate both the offensive techniques used to validate a weakness and the defensive controls required to reduce the associated risk.

> **Ethical scope:** All activities documented in this repository were performed in an authorized, isolated lab environment for educational and defensive purposes. No production system or third-party service was targeted.

---

## Author and Course Information

- **Student:** Udam Akume Gabriel
- **Student ID:** 2025/CFP/6250
- **Course:** Advanced Web Application Security (WASF1203)
- **Instructor:** Mr. Faisa Imran
- **Original submission date:** 4 June 2025
- **Focus area:** Practical web application vulnerability assessment and mitigation

---

## Objectives

The primary objectives of the lab series were to:

- Build practical experience in web application security testing.
- Understand how application vulnerabilities arise in client-side and server-side code.
- Perform manual and automated vulnerability assessment in an authorized environment.
- Validate security weaknesses using controlled proof-of-concept testing.
- Examine the possible impact of vulnerabilities on confidentiality, integrity, and availability.
- Implement secure coding and configuration controls.
- Compare vulnerable components with remediated versions.
- Improve technical documentation, evidence handling, and security reporting skills.

---

## Lab Environment

The practical work was performed in an isolated virtual environment.

| Component | Purpose |
|---|---|
| Windows 11 | Host operating system |
| VMware Workstation 17 Player | Virtualization platform used to isolate the lab |
| Kali Linux | Security testing and command-line environment |
| XAMPP | Local Apache, PHP, and database stack |
| Localhost vulnerable application | Authorized target used to simulate application weaknesses |
| Web browser and developer tools | Request inspection, client-side analysis, and testing |
| Microsoft Word | Initial lab documentation and reporting |

---

## Assessment Methodology

The exercises followed a structured security-testing workflow:

1. **Environment preparation**  
   The virtual machine, local server, database, and intentionally vulnerable application were configured in an isolated environment.

2. **Application mapping**  
   Application pages, inputs, parameters, authentication workflows, sessions, and protected resources were reviewed to understand the attack surface.

3. **Manual testing**  
   Inputs and application behavior were examined manually to identify validation weaknesses, access-control failures, authentication issues, and unsafe server-side processing.

4. **Automated assessment**  
   Authorized security-testing tools were used where appropriate to supplement manual analysis and compare results.

5. **Controlled validation**  
   Suspected weaknesses were validated using limited proof-of-concept tests designed to demonstrate impact without causing unnecessary damage.

6. **Source-code and configuration review**  
   Vulnerable back-end logic and relevant server settings were inspected to identify root causes.

7. **Mitigation**  
   Secure coding practices, access checks, safer parsers, session protections, and configuration improvements were applied.

8. **Retesting**  
   Remediated components were tested again to determine whether the security controls addressed the original weakness.

9. **Documentation**  
   Each lab was documented with steps, observations, sanitized screenshots, mitigation notes, and lessons learned.

---

# Labs Completed

## 1. Cross-Site Scripting

### Scope

The Cross-Site Scripting lab examined how untrusted input may be returned to a browser or processed by client-side code without appropriate validation and output encoding.

### Activities

- Identified user-controlled inputs and output locations.
- Tested application behavior using non-destructive proof-of-concept input.
- Compared reflected, stored, and client-side XSS concepts where applicable.
- Reviewed the output context in HTML and browser-side processing.
- Examined the security impact on users and application sessions.
- Applied contextual output encoding and safer input-handling practices.
- Retested the application after remediation.

### Security impact

Successful XSS can allow unauthorized script execution in a user's browser, interface manipulation, exposure of accessible application data, or unauthorized actions within the user's session.

### Recommended controls

- Apply context-aware output encoding.
- Use secure templating frameworks that escape output by default.
- Validate input using allowlists where practical.
- Avoid unsafe DOM manipulation functions.
- Implement a restrictive Content Security Policy as defense in depth.
- Protect session cookies with appropriate security attributes.

---

## 2. Advanced SQL Injection: Manual Testing

### Scope

The manual SQL injection lab focused on understanding how unsafe construction of database queries can allow user input to alter query logic.

### Activities

- Identified parameters that interacted with the database.
- Observed application responses and database-related error behavior.
- Performed controlled manual testing against the local lab application.
- Examined differences between visible-output and inference-based behavior.
- Assessed the possible effect on authentication and data access.
- Reviewed vulnerable back-end query construction.
- Replaced unsafe query construction with parameterized statements.
- Retested the remediated application.

### Security impact

SQL injection may allow unauthorized access to application data, authentication bypass, modification or deletion of records, and, under dangerous server configurations, broader compromise of the application environment.

### Recommended controls

- Use prepared statements and parameterized queries.
- Avoid constructing SQL commands through string concatenation.
- Apply least privilege to database accounts.
- Return generic errors to users while securely logging diagnostic details.
- Validate data types, expected formats, and input length.
- Keep database software and application dependencies updated.

---

## 3. Advanced SQL Injection: Automated Testing

### Scope

This lab used authorized automated testing to supplement manual SQL injection analysis and demonstrate the importance of validating automated results.

### Activities

- Configured the tool for the isolated lab target.
- Evaluated selected parameters for injection behavior.
- Compared automated output with manual observations.
- Reviewed false-positive and false-negative considerations.
- Limited testing to the defined lab scope.
- Documented findings and corresponding defensive controls.

### Key lesson

Automated tools can improve consistency and speed, but they do not replace manual verification, application-context analysis, or professional judgment.

### Recommended controls

The principal controls are parameterized queries, least-privileged database access, secure error handling, input validation, monitoring, and remediation verification.

---

## 4. Broken Authentication

### Scope

The authentication lab examined weaknesses in login controls, credential handling, account workflows, and identity verification.

### Activities

- Reviewed login and logout behavior.
- Examined password handling and authentication responses.
- Tested for inconsistent server-side enforcement.
- Assessed account and identity workflows in the local application.
- Reviewed whether errors exposed unnecessary information.
- Implemented stronger authentication logic and safer feedback.
- Retested the secured workflow.

### Security impact

Broken authentication can lead to account takeover, unauthorized access, impersonation, and exposure of protected application functions or data.

### Recommended controls

- Use trusted authentication frameworks.
- Store passwords with an approved adaptive password-hashing algorithm.
- Apply rate limiting and monitoring to repeated authentication failures.
- Use multi-factor authentication where appropriate.
- Prevent account enumeration through consistent responses.
- Reauthenticate users before sensitive operations.
- Invalidate sessions correctly after logout and credential changes.

---

## 5. Insecure Direct Object References and IDOR

### Scope

This lab examined whether changing a user-controlled object identifier could provide access to another user's data or resources. IDOR is a common form of broken object-level authorization.

### Activities

- Identified object references in routes, parameters, forms, and requests.
- Tested access using accounts with different authorization levels.
- Modified references in controlled requests to evaluate server-side enforcement.
- Distinguished authentication from authorization.
- Added object-level authorization checks.
- Retested both permitted and prohibited access scenarios.

### Security impact

IDOR can expose, modify, or delete records belonging to other users and can undermine confidentiality and integrity across the application.

### Recommended controls

- Enforce authorization on every server-side request.
- Verify that the authenticated user is permitted to access the requested object.
- Deny access by default.
- Centralize access-control logic where practical.
- Avoid treating unpredictable identifiers as an authorization control.
- Log and monitor denied or unusual object-access attempts.

---

## 6. Session Mismanagement and Session Hijacking Risks

### Scope

The session-security lab reviewed how the application created, stored, transmitted, rotated, and invalidated session identifiers.

### Activities

- Inspected session creation during authentication.
- Reviewed cookie properties and session lifecycle behavior.
- Tested logout and invalidation behavior.
- Examined session rotation after authentication or privilege changes.
- Assessed timeout and concurrent-session considerations.
- Applied stronger cookie and server-side session controls.
- Retested session behavior after remediation.

### Security impact

Weak session management can allow unauthorized reuse of a session, account impersonation, persistence after logout, or access from an unintended context.

### Recommended controls

- Generate high-entropy session identifiers.
- Transmit sessions only over HTTPS.
- Apply `Secure`, `HttpOnly`, and appropriate `SameSite` cookie attributes.
- Rotate session identifiers after authentication and privilege changes.
- Enforce idle and absolute expiration.
- Invalidate server-side sessions during logout.
- Avoid placing session identifiers in URLs.

---

## 7. XML External Entity Processing

### Scope

The XML External Entity lab examined the risk created when an XML parser permits unsafe external entity resolution or dangerous document type definitions.

### Activities

- Identified application functionality that processed XML input.
- Reviewed parser behavior in the controlled lab.
- Used a limited proof of concept to validate unsafe XML processing.
- Assessed potential file-disclosure and server-side request risks conceptually.
- Disabled unnecessary external entity and DTD processing.
- Applied safer parser configuration and input restrictions.
- Retested the secured implementation.

### Security impact

Unsafe XML parsing can contribute to local file disclosure, server-side request forgery, denial of service, or exposure of internal resources, depending on parser behavior and system permissions.

### Recommended controls

- Disable external entity resolution and DTD processing when not required.
- Use maintained parsers with secure defaults.
- Validate XML against a strict schema where appropriate.
- Restrict network and file-system permissions available to the application.
- Apply time, memory, and input-size limits.
- Prefer simpler data formats when advanced XML features are unnecessary.

---

## Additional Vulnerability Areas Explored

The broader course exercises also introduced or examined the following areas:

- Logging and monitoring weaknesses
- Broken access control
- Security misconfiguration
- HTML injection
- Operating-system command injection
- Sensitive-data exposure
- Insecure deserialization

For these topics, public documentation should focus on root cause, business impact, secure implementation, and sanitized evidence. Detailed payloads or reusable offensive components should remain restricted where publication could create unnecessary risk.

---

## Vulnerable and Secured Implementations

Where applicable, the lab materials separate the application into two versions:

- **Task 1:** Vulnerable implementation used to understand and validate the weakness.
- **Task 2:** Secured implementation containing the relevant mitigation.

This comparison demonstrates not only how a vulnerability may be identified, but also how server-side controls can reduce the risk. Lab 12 builds on Lab 11 by presenting a mitigated version of the earlier vulnerable implementation.

---

## Risk Classification Template

Use a consistent format for documenting validated findings:

## Key Lessons Learned

- Manual testing is essential for understanding application context and business logic.
- Automated tools are valuable for coverage but require careful configuration and validation.
- Authentication confirms identity, while authorization determines permitted actions.
- Client-side restrictions cannot replace server-side security controls.
- Secure session management requires protection throughout the complete session lifecycle.
- Error messages can expose implementation details and should be handled carefully.
- Database privileges and application permissions should follow least-privilege principles.
- Remediation is incomplete until the application has been retested.
- Clear evidence and reproducible documentation are important parts of professional security work.
- Secure development is more effective when security controls are incorporated from the design stage.

---

## Skills Demonstrated

- Web application attack-surface analysis
- Manual vulnerability assessment
- Authorized automated security testing
- Input-validation and output-encoding analysis
- SQL injection assessment and remediation
- Authentication and authorization testing
- Object-level access-control testing
- Session-security assessment
- XML parser security review
- Vulnerable and secured code comparison
- Risk analysis and remediation planning
- Evidence collection and technical reporting
- Ethical lab practice and responsible disclosure awareness

---

## Limitations

- Testing was limited to a controlled local lab environment.
- Findings should not be interpreted as results from a production penetration test.
- Some behavior may depend on the specific versions and configurations used in the lab.
- Public evidence is intentionally sanitized.
- This repository emphasizes learning outcomes and defensive remediation rather than weaponized exploitation.

---

## Responsible Use

The content in this repository is provided strictly for authorized education, defensive research, and secure-development practice. Anyone using these materials is responsible for obtaining explicit permission before testing any system they do not own.

Do not use the material to access data, accounts, applications, or infrastructure without authorization. Follow applicable laws, organizational policies, course requirements, and responsible-disclosure practices.

---

## Acknowledgment

The labs were completed under the guidance of **Mr. Faisa Imran** as part of the **Advanced Web Application Security (WASF1203)** course. The practical exercises strengthened my understanding of how web vulnerabilities are introduced, validated, mitigated, and documented.

---

## Contact

**Udam Akume Gabriel**  
Digital Forensics and Incident Response | Web Application Security

- GitHub: `https://github.com/UdamGabriel`
- LinkedIn: `www.linkedin.com/in/udam-akume-gabriel-09a8bb334`
- Email: `udamgabrielakume99@gmail.com`
