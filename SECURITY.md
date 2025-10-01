# Security Policy

## Supported Versions

We release patches for security vulnerabilities for the following versions:

| Version | Supported          |
| ------- | ------------------ |
| 0.0.18  | :white_check_mark: |
| < 0.0.18| :x:                |

## Reporting a Vulnerability

We take the security of the Nord Pool integration seriously. If you have discovered a security vulnerability, please report it to us privately.

**Please do NOT open a public issue for security vulnerabilities.**

### How to Report

1. **Email**: Send details to the repository maintainer via GitHub (create a security advisory via the Security tab)
2. **Expected Response Time**: You should receive an initial response within 48 hours
3. **Disclosure Timeline**: We aim to patch critical vulnerabilities within 7 days

### What to Include

When reporting a vulnerability, please include:

- Description of the vulnerability
- Steps to reproduce the issue
- Potential impact
- Suggested fix (if you have one)
- Your contact information

### What Happens Next

1. We will acknowledge receipt of your vulnerability report
2. We will investigate and validate the issue
3. We will develop and test a fix
4. We will release a patched version
5. We will publicly disclose the vulnerability (with credit to you, if desired)

## Security Best Practices

When using this integration:

1. Keep the integration updated to the latest version
2. Review the CHANGELOG for security-related updates
3. Follow Home Assistant security best practices
4. Do not expose your Home Assistant instance directly to the internet without proper authentication
5. Use strong passwords and enable two-factor authentication for your Home Assistant account

## Security Considerations

This integration:

- Communicates with Nord Pool's public API over HTTPS
- Does not store sensitive credentials (no authentication required for Nord Pool API)
- Does not expose any external ports or services
- Runs within the Home Assistant security context

## Contact

For security concerns, please use GitHub's security advisory feature or contact the maintainer directly through GitHub.
