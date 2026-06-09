# SECURITY.MD - Security Policy
# ComposerAtlas

**Version:** 1.1
**Last Updated:** 2026-06-08

---

## Overview

ComposerAtlas is a fully static, browser-only website with no server infrastructure, no user accounts, no authentication, and no database service. This architecture significantly limits the attack surface. This document outlines the security posture, known considerations, and responsible disclosure policy.

---

## Architecture Security Posture

| Concern | Status | Notes |
|---|---|---|
| Server-side vulnerabilities | N/A | No server exists |
| SQL injection | N/A | No database service |
| Authentication bypass | N/A | No auth layer |
| Secret/key exposure | Low risk | No API keys in client code |
| XSS (Cross-Site Scripting) | Managed | See below |
| Supply chain attacks | Low risk | Minimize dependencies |
| Data exfiltration | N/A | No user data collected |
| HTTPS | Required | GitHub Pages enforces HTTPS |

---

## Key Security Practices

### No Secrets in Client Code
- No API keys, tokens, or credentials are stored in the repository or rendered in client-side JavaScript
- The Composer.trade symphony URLs are public by nature and safe to expose

### JSON Data Integrity
- `/data/strategies.json` is the only data source and is committed to the public repo
- All changes to JSON go through GitHub's commit history, providing a full audit trail
- Do not include any personally identifiable information (PII) in strategy entries

### XSS Prevention
- All dynamic content rendered from JSON must be escaped before DOM insertion
- Do not use `innerHTML` with unsanitized JSON values; use `textContent` or framework-native safe rendering
- Strategy descriptions and names in JSON should not contain HTML tags

### Dependency Management
- ComposerAtlas has **zero npm/Node.js dependencies** — no `package.json`, no `node_modules`, no supply chain risk from npm packages
- External resources are limited to: Google Fonts CDN (fonts only; no JS)
- If a CDN dependency is ever added, it must be loaded from an official, well-maintained source with SRI (Subresource Integrity) hash verification

### External Links
- All links to Composer.trade and external resources must use `target="_blank"` with `rel="noopener noreferrer"` to prevent tab-napping

### GitHub Repository
- Repository is public; treat all committed content as fully public
- Never commit `.env` files, credentials, or sensitive personal information
- Branch protection on `main` is recommended: require PR review before merge

---

## Google AdSense (Post-MVP)

When AdSense is integrated:
- Use only the official Google AdSense script tag
- Do not load ad scripts from unverified third-party sources
- Review AdSense policy compliance before enabling

---

## Donation Link

The support page links to https://azqato.github.io/support.html, an external page. Ensure that page is maintained and does not collect data beyond what is necessary for the donation flow.

---

## Responsible Disclosure

If you discover a security issue with ComposerAtlas (e.g., malicious content injection via JSON, broken external link pointing to a compromised domain), please report it by opening a GitHub Issue tagged `security` or contacting the repository owner directly.

We will acknowledge reports within 72 hours and address confirmed issues in the next available release.

---

## Out of Scope

The following are explicitly out of scope for ComposerAtlas security considerations:
- Security of Composer.trade (third-party platform)
- Security of the user's Composer.trade account
- Investment losses resulting from using featured strategies
