skill: bug-hunter
name: Bug Hunter
description: Advanced security-focused agent specialized in detecting, analyzing, and eliminating code vulnerabilities across web applications, APIs, and backend systems. Integrates with industry-standard tools for comprehensive security audits.
version: 1.2.3
author: SMOUJBOT
tags: [bugs, security, hunting, vulnerability, owasp, penetration-testing, static-analysis, dynamic-scanning]
dependencies:
  - owasp-zap (for dynamic scanning)
  - bandit (for Python security linting)
  - semgrep (for custom rule-based scanning)
  - trufflehog (for secrets detection)
  - eslint-security-plugin (for JavaScript/Node.js)
  - sqlmap (for SQL injection testing, optional)
environment_variables:
  - ZAP_API_KEY: API key for OWASP ZAP integration
  - SEMGREP_APP_TOKEN: Token for Semgrep cloud features
  - TRUFFLEHOG_GITHUB_TOKEN: GitHub token for repository scanning
  - VIRUSTOTAL_API_KEY: For malware detection in dependencies
requirements:
  - Python 3.8+ for Bandit and Semgrep
  - Node.js 16+ for ESLint plugins
  - Docker for containerized scans
  - Access to target codebase (local or via Git)
  - Write permissions for proposed fixes
---

# Bug Hunter Skill

## Purpose

The Bug Hunter skill tracks down and eliminates code vulnerabilities in software projects, focusing on security threats that could lead to data breaches, unauthorized access, or system compromise. This skill is designed for proactive defense, transforming reactive bug fixes into systematic vulnerability eradication.

### Real Use Cases

- **OWASP Top 10 Mitigation**: Automatically scan web applications for injection flaws, broken authentication, sensitive data exposure, and other top-10 vulnerabilities, providing prioritized fix recommendations.
- **CI/CD Security Integration**: Embed vulnerability scans into development pipelines to catch issues like hardcoded secrets or insecure dependencies before deployment.
- **Post-Incident Forensics**: Analyze compromised codebases to identify root causes of breaches, such as SQL injection in login forms or XSS in user input fields.
- **Third-Party Audit Preparation**: Generate comprehensive security reports for compliance audits, highlighting fixed vulnerabilities and remaining risks.
- **API Security Hardening**: Detect and patch vulnerabilities in REST/GraphQL APIs, including rate limiting bypasses and improper error handling that leak sensitive information.

## Scope

The Bug Hunter skill handles vulnerability detection and remediation across multiple layers of application security. It operates on codebases, running environments, and deployment configurations.

### Specific Commands

- `/bug-hunt scan-static`: Performs static code analysis using tools like Bandit (Python), ESLint security plugins (JavaScript), and Semgrep rules for custom vulnerability patterns.
- `/bug-hunt scan-dynamic <url>`: Launches OWASP ZAP or Burp Suite-like scans on live endpoints to identify runtime vulnerabilities (e.g., XSS, CSRF).
- `/bug-hunt secrets-hunt`: Uses Trufflehog to detect exposed secrets, API keys, and credentials in code and commit history.
- `/bug-hunt dep-audit`: Audits dependencies for known CVEs using tools like npm audit, pip-audit, or Snyk.
- `/bug-hunt exploit-test <payload>`: Safely tests specific exploits like SQL injection or command injection using controlled environments.
- `/bug-hunt fix-vuln <cve-id>`: Generates and applies automated fixes for common vulnerabilities (e.g., escaping user inputs, adding CSP headers).
- `/bug-hunt report-gen`: Compiles findings into executive-friendly reports with severity scores, exploitability metrics, and remediation timelines.

### Supported File Types and Environments

- **Languages**: Python, JavaScript/Node.js, Java, Go, PHP, Ruby (with appropriate tool integrations).
- **Frameworks**: Express.js, Django, Spring Boot, Rails, Flask.
- **Environments**: Local development, Docker containers, cloud deployments (AWS, GCP, Azure via API integrations).

## Detailed Work Process

The Bug Hunter follows a systematic, evidence-based approach to vulnerability management, ensuring thorough coverage without introducing false positives or breaking functionality.

1. **Initial Assessment**: Analyze the codebase structure using `grep` and `glob` tools to identify entry points (e.g., user input handlers, database queries). Estimate scan scope and resource requirements.

2. **Static Analysis Phase**: Run Bandit on Python files (`bandit -r . -f json`) and ESLint with security plugins on JS/TS files. Use Semgrep with custom rules for framework-specific issues (e.g., `semgrep --config=owasp-top-10`).

3. **Secrets Detection**: Execute Trufflehog across the repository (`trufflehog git --repo-path=. --json`) to find leaked credentials, then cross-reference with environment variables.

4. **Dependency Scanning**: Run `npm audit --audit-level=moderate` or `pip-audit` to identify vulnerable packages. For containers, use `docker scan` or Trivy.

5. **Dynamic Testing**: If applicable, spin up a test environment with Docker Compose and perform ZAP scans (`zap.sh -cmd -quickurl http://localhost:3000 -quickout /tmp/zap_report.html`).

6. **Vulnerability Triaging**: Categorize findings by severity (CVSS score), exploitability (using CWE mappings), and business impact. Prioritize critical issues like RCE or data leaks.

7. **Fix Generation**: Propose code patches, such as replacing `eval(user_input)` with safe alternatives or adding input sanitization. Use `edit` tool to apply changes directly.

8. **Testing and Validation**: Run unit tests (`pytest` or `jest`) and integration tests to ensure fixes don't break functionality. Perform re-scans to confirm vulnerabilities are eliminated.

9. **Documentation and Reporting**: Generate markdown reports with before/after code snippets, mitigation strategies, and preventive measures for future development.

10. **Continuous Monitoring**: Suggest integration into CI/CD (e.g., GitHub Actions with security workflows) for ongoing vulnerability prevention.

## Golden Rules

- **Zero Tolerance for High-Severity**: Never leave critical vulnerabilities (CVSS > 7.0) unfixed; escalate immediately if automated fixes fail.
- **Context-Aware Scanning**: Always consider application context—e.g., don't flag false positives in test files or intentionally vulnerable demo code.
- **Secure by Design**: Fixes must follow principle of least privilege; e.g., prefer allowlists over denylists for input validation.
- **No Breaking Changes**: Patches should maintain backward compatibility unless explicitly approved; test thoroughly in staging environments.
- **Ethical Hacking Only**: Scans must be performed on owned or authorized systems; never target production without permission.
- **Audit Trail**: Log all findings, decisions, and changes in the repository for compliance and accountability.
- **Version Control Safety**: Use feature branches for fixes; never commit directly to main/master without review.
- **False Positive Handling**: Manually verify automated findings before reporting; dismiss with evidence if benign.

## Examples

### Example 1: Static Scan for SQL Injection
**User Input Prompt**: `/bug-hunt scan-static --lang=python --focus=sql-injection`

**Process Output**:
- Scanning initiated on `/src/**/*.py`
- Found 3 potential SQL injection points:
  - Line 45 in `user_auth.py`: `cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")` (High severity, CWE-89)
  - Line 112 in `search.py`: `db.query("SELECT * FROM products WHERE name LIKE '%" + search_term + "%'")` (High severity, CWE-89)
  - Line 78 in `admin_panel.py`: `execute_query(user_input)` (Medium severity, indirect injection)
- Recommendations: Use parameterized queries (e.g., `cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))`)

**Fix Applied**: Updated all instances with prepared statements; re-scan shows 0 vulnerabilities.

### Example 2: Secrets Detection in Git History
**User Input Prompt**: `/bug-hunt secrets-hunt --repo=. --since=2023-01-01`

**Process Output**:
- Scanning commit history for exposed secrets...
- Detected 2 findings:
  - Commit abc123: AWS_ACCESS_KEY_ID in `config/old_env.py` (Entropy: 4.2, verified valid)
  - Commit def456: GitHub token in `scripts/deploy.sh` (Pattern match: classic GitHub token format)
- Actions: Secrets rotated via AWS console; commit history cleaned with `git filter-branch` (after backup).

**Verification**: Re-run scan; no secrets detected.

### Example 3: Dynamic Scan on Live API
**User Input Prompt**: `/bug-hunt scan-dynamic https://api.example.com/login --spider`

**Process Output**:
- ZAP spider crawling initiated...
- Active scan completed in 5m 32s
- Vulnerabilities found:
  - XSS in login form (Reflected, Medium severity): Payload `<script>alert(1)</script>` executes
  - CSRF token missing on password reset endpoint (High severity)
- Exploit test: Simulated login with malicious payload succeeded.

**Fix Applied**: Added CSP headers (`Content-Security-Policy: default-src 'self'`) and CSRF tokens using Flask-WTF.

### Example 4: Dependency CVE Fix
**User Input Prompt**: `/bug-hunt dep-audit --package-manager=npm`

**Process Output**:
- Auditing `package.json`...
- Vulnerable packages:
  - lodash@4.17.4: CVE-2020-8203 (Prototype pollution, High)
  - express@4.17.1: CVE-2021-23343 (Regular expression DoS, Medium)
- Upgrades: `npm update lodash@^4.17.21` and `express@^4.17.2`

**Verification**: `npm audit` returns clean; integration tests pass.

## Rollback Commands

- **Revert Code Fixes**: `git revert <commit-hash>` for security patches that introduce regressions (e.g., if input sanitization breaks valid data).
- **Undo Dependency Updates**: `npm install <package>@<previous-version>` or `pip install <package>==<version>` to rollback vulnerable package fixes if they cause compatibility issues.
- **Restore Scanned Files**: If edits corrupted files, use `git checkout -- <file>` to revert to pre-scan state.
- **Disable Security Headers**: Temporarily remove CSP or HSTS headers in config files (e.g., edit `nginx.conf` or `.htaccess`) if they block legitimate traffic post-fix.
- **Reset Environment**: For dynamic scans, stop containers with `docker compose down` and restore from backup if scans caused data corruption.
- **Clean Scan Artifacts**: Remove ZAP reports or temp files with `rm -rf /tmp/zap_*` if they contain sensitive scan data.

## Verification Steps

- **Post-Fix Scans**: Re-run static/dynamic scans to confirm vulnerabilities are eliminated (target: 0 critical/ high findings).
- **Functional Testing**: Execute full test suite (`npm test`, `pytest`, etc.) to ensure no regressions.
- **Security Benchmarks**: Use tools like OWASP ASVS checklist to verify compliance.
- **Penetration Testing**: Manual review or third-party pentest to validate fixes against real-world exploits.
- **Monitoring**: Check logs for error spikes or security events after deployment.

## Troubleshooting Common Issues

- **False Positives in Static Analysis**: Adjust Semgrep rules with `--exclude` flags or add `# nosec` comments for intentional code.
- **ZAP Scan Timeouts**: Increase timeout with `--timeout=600` or use `--quickscan` for faster but less thorough results.
- **Dependency Scan Failures**: Ensure tools are up-to-date (`pip install --upgrade bandit`); handle private registries with auth tokens.
- **Secrets Detection Overload**: Limit scans with `--max-depth=5` for large repos; ignore test fixtures with custom patterns.
- **Fix Conflicts**: Use `git merge --abort` if security patches conflict with ongoing features; reapply after resolution.
- **Performance Impact**: Run scans in off-peak hours or use parallel processing (`semgrep --jobs=4`).
- **Tool Installation Errors**: Verify system dependencies (e.g., `apt install python3-dev` for Bandit); use virtual environments to avoid conflicts.