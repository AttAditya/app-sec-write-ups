# Application Security CTF Write-up

**Name:** Aditya Prasad Dash  
**Roll Number:** 23BCS1013

---

# Challenge 1: Vault Access

## Description

A SQL Injection vulnerability was identified in the Vault Access functionality. User-supplied input was incorporated directly into a database query without proper sanitization or parameterization.

By injecting SQL syntax into the authentication field, it was possible to alter the logic of the query and bypass authentication checks. This resulted in unauthorized access to protected vault data.

## Steps to Reproduce

1. Navigate to the Vault Access page.
2. Locate the authentication or access input field.
3. Submit the following payload:

```text
' OR 1=1 --
```

4. Submit the request.
5. Observe that the authentication check is bypassed.
6. Access the protected vault contents.
7. Retrieve the flag:

```text
FLAG{vault_d18ead79}
```

## CVSS Score

**Base Score:** 9.8

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

## Impact

An attacker could bypass authentication controls and gain unauthorized access to sensitive information. Depending on database permissions, an attacker could view customer records, access financial data, modify account information, or perform administrative actions.

## Remediation

- Use parameterized queries (prepared statements).
- Never concatenate user input directly into SQL queries.
- Apply server-side input validation.
- Enforce least-privilege permissions on database accounts.
- Perform regular security testing for injection vulnerabilities.

---

# Challenge 2: Unsigned Access

## Description

An OS Command Injection vulnerability was identified in the statement generation functionality located at `/tools/statement`.

The application executed user-controlled input within an operating system command without proper validation. This allowed arbitrary command execution on the server.

Using this vulnerability, sensitive files including the application's Dockerfile and environment configuration were accessed. These files contained the JWT signing secret used by the application. With the secret exposed, valid JWT tokens could be forged and used to access protected endpoints.

## Steps to Reproduce

1. Navigate to:

```text
http://localhost:3000/tools/statement
```

2. Inject operating system commands using shell metacharacters.
3. Enumerate files and directories on the server.
4. Read the Dockerfile and environment configuration files.
5. Extract the JWT signing secret from the application configuration.
6. Create a JWT containing elevated privileges and sign it using the recovered secret.
7. Send requests to protected API endpoints using the forged token.
8. Observe successful authentication and retrieve:

```text
FLAG{jwt_f5852cfa}
```

## CVSS Score

**Base Score:** 9.8

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

## Impact

An attacker could execute arbitrary commands on the server, obtain application secrets, forge authentication tokens, impersonate privileged users, access sensitive information, and potentially gain complete control of backend systems.

## Remediation

- Eliminate OS command injection vulnerabilities.
- Store secrets in a dedicated secrets management solution.
- Rotate compromised JWT signing keys immediately.
- Restrict access to configuration files and environment secrets.
- Apply the principle of least privilege.
- Monitor for unauthorized token generation.

---

# Challenge 3: Import Statement

## Description

An OS Command Injection vulnerability was identified in the Import Statement functionality. User-supplied input was incorporated into a system command without proper sanitization, allowing arbitrary operating system commands to be executed on the server.

By leveraging shell metacharacters, an attacker could enumerate directories, inspect application files, and access sensitive information stored on the host.

## Steps to Reproduce

1. Navigate to the Import Statement functionality.
2. Provide input containing a shell command separator followed by an operating system command.
3. Submit the request.
4. Observe that the command executes on the server.
5. Enumerate directories and inspect application files.
6. Locate the flag file within the application data directory.
7. Read the file contents to obtain:

```text
FLAG{xxe_3986bc89}
```

## CVSS Score

**Base Score:** 9.8

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

## Impact

An attacker could execute arbitrary commands on the server, read confidential records, obtain credentials, modify application data, and potentially gain complete control of the underlying system.

## Remediation

- Avoid constructing shell commands using user-controlled input.
- Use parameterized process execution APIs.
- Validate input against strict allow-lists.
- Execute services with least privilege.
- Implement monitoring for unexpected command execution.

---

# Challenge 4: Welcome Card

## Description

An OS Command Injection vulnerability was discovered in the Welcome Card generation feature. Insufficient input validation allowed user-supplied data to be interpreted by the operating system shell.

An attacker could inject additional commands and execute them with the privileges of the application process.

## Steps to Reproduce

1. Open the Welcome Card feature.
2. Supply input containing a command separator followed by a system command.
3. Submit the request.
4. Observe command execution in the server response.
5. Enumerate accessible files and directories.
6. Access the application data directory.
7. Read the flag file and obtain:

```text
FLAG{ssti_b7942621}
```

## CVSS Score

**Base Score:** 9.8

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

## Impact

Successful exploitation could allow attackers to access sensitive information, alter application data, execute arbitrary commands, and compromise the server hosting the application.

## Remediation

- Never pass unsanitized user input to shell commands.
- Use safe library functions instead of shell execution.
- Apply strict input validation and encoding.
- Restrict application privileges.
- Perform regular security testing for injection flaws.

---

# Challenge 5: Statement Mailer

## Description

An OS Command Injection vulnerability was present in the Statement Mailer functionality. User-controlled input was processed by a backend system command without adequate sanitization.

This enabled attackers to inject arbitrary shell commands and interact with the server's file system.

## Steps to Reproduce

1. Navigate to the Statement Mailer feature.
2. Enter input containing a shell metacharacter followed by an operating system command.
3. Submit the request.
4. Confirm that the injected command executes successfully.
5. Browse application directories and inspect stored files.
6. Locate and read the flag file.
7. Retrieve:

```text
FLAG{cmdi_fb5b4095}
```

## CVSS Score

**Base Score:** 9.8

**Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

## Impact

An attacker could gain unauthorized access to financial records, extract credentials, modify business data, disrupt services, or take full control of the affected server.

## Remediation

- Eliminate shell command construction using user input.
- Use parameterized process execution APIs.
- Implement strict server-side validation.
- Apply least-privilege access controls.
- Monitor and alert on suspicious command execution activity.

---

# Flags Obtained

```text
FLAG{vault_d18ead79}
FLAG{jwt_f5852cfa}
FLAG{cmdi_fb5b4095}
FLAG{ssti_b7942621}
FLAG{xxe_3986bc89}
```
