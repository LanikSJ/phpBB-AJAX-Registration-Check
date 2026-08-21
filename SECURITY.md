# Security Policy

## Supported Versions

The following table lists the versions of this project currently supported with
security updates:

| Version | Supported          |
| ------- | ------------------ |
| 1.0.x   | :white_check_mark: |
| < 1.0   | :x:                |

## Reporting a Vulnerability

We take security seriously and appreciate your efforts to responsibly disclose
your findings.

### How to Report

**Do NOT open a public issue** for security vulnerabilities. Instead, please
report security issues through one of these channels:

1. **GitHub Security Advisories** (Preferred): [Report via
   GitHub](https://github.com/LanikSJ/phpBB-AJAX-Registration-Check/security/advisories/new)
2. **Email**: Send details to [security@lanik.us](mailto:security@lanik.us)
3. **Security Discussions**: Open a discussion in our
   [GitHub Discussions](https://github.com/LanikSJ/phpBB-AJAX-Registration-Check/discussions/categories/security)

### What to Include

When reporting a vulnerability, please include:

- **Description**: Clear explanation of the security issue
- **Steps to Reproduce**: Detailed steps to reproduce the vulnerability
- **Impact Assessment**: Potential impact and affected components
- **Proof of Concept**: If applicable, a minimal reproduction case
- **Suggested Fix**: If you have ideas for a fix (optional)

### Response Timeline

We are committed to responding to security reports in a timely manner:

- **Initial Response**: Within 48 hours of receiving the report
- **Status Update**: Within 5 business days with assessment
- **Resolution**: We will work diligently to fix critical vulnerabilities as
  quickly as possible

### Responsible Disclosure

We ask that you:

- Give us reasonable time to investigate and fix the issue before public
  disclosure
- Do not access, modify, or delete user data
- Do not perform attacks that could harm the availability of our services
- Do not publicly disclose the vulnerability until we have had a chance to
  address it

## Security Considerations

### Project-Specific Security

This project is a phpBB extension that validates registration form data via
AJAX. Security considerations include:

- **Server-Side Validation is Authoritative**: The AJAX checks are a
  convenience for users. phpBB's own server-side validation remains the
  authoritative check and must never be bypassed or weakened by this extension.
- **SQL Injection Protection**: All database queries use phpBB's
  `sql_escape()` and `utf8_clean_string()` helpers to safely handle user input
  before it is used in queries.
- **XSS Protection**: All values embedded into inline JavaScript are encoded
  with `json_encode()` using `JSON_HEX_TAG | JSON_HEX_APOS | JSON_HEX_QUOT |
  JSON_HEX_AMP` to prevent script injection.
- **Request Validation**: The AJAX controller only responds to POST requests
  that are flagged as AJAX requests, and rejects invalid query types.
- **Client-Side Checks are UX Only**: Password strength and format checks run
  in the browser are for user feedback only and must not be relied upon for
  security enforcement.
- **No Sensitive Data Exposure**: The extension only checks usernames and
  email addresses for availability and validity; it does not store, log, or
  transmit any sensitive user data.

## Security Best Practices

### For Users

- **Keep Updated**: Always use the latest version of the extension and keep
  your phpBB installation up to date
- **Verify Sources**: Only download the extension from official sources, such
  as the [phpBB extension database](https://www.phpbb.com/customise/db/extension/ajax_registration_check/)
- **Report Suspicious Behavior**: If you notice anything unusual, please report
  it

### For Developers

When contributing to the project:

- **Validate Input**: Always use phpBB's request and database helpers to
  validate and escape user input
- **Encode Output**: Ensure any value embedded into templates or JavaScript is
  properly encoded to prevent XSS
- **Follow Guidelines**: Adhere to the project's contribution guidelines and
  phpBB extension development best practices
- **Security First**: Prioritize security when adding new features or checks

## Security Resources

- [phpBB Security](https://www.phpbb.com/security/)
- [phpBB Extension Development Documentation](https://area51.phpbb.com/docs/dev/)
- [GitHub Security Documentation](https://docs.github.com/en/code-security/getting-started)

## Contact

For general security questions or concerns, you can:

- Open a discussion in our
  [GitHub Discussions](https://github.com/LanikSJ/phpBB-AJAX-Registration-Check/discussions)
- Contact the maintainer (`@LanikSJ`) directly or through the security email
  above for sensitive matters

Thank you for helping keep phpBB AJAX Registration Check secure!
