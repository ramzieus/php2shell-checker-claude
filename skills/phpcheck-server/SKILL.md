---
name: phpcheck-server
description: Check php2shell patch status of the PHP serving a site, with local or shell access to the server. Use when the user asks whether the PHP on their own server/host is patched or vulnerable to php2shell, or wants the installed PHP version verified against advisories.
allowed-tools: Bash(php -v), Bash(php-fpm* -v), Bash(apachectl *), Bash(dpkg -l *), Bash(rpm -q *), Bash(node *phpcheck.js*)
---

Determine the PHP version that actually serves the site, then judge its patch status.

## Steps

1. Get the version: run `php -v` (or `node "${CLAUDE_SKILL_DIR}/../phpcheck-url/scripts/phpcheck.js" --local --json`).
2. Confirm it is the PHP serving the site, not just the CLI default. Check the PHP-FPM binary (`php-fpm -v`, `php-fpm8.x -v`) or the Apache `libphp` module. Several PHP versions often coexist.
3. Compare against the table below.
4. Check the full package revision against the vendor advisory (Debian/Ubuntu `dpkg -l 'php*'`, RHEL `rpm -q php`). Vendor backports can fix an older version string.
5. Report: version, patch status, required update. If the serving version cannot be verified, report `unknown`.

## Vulnerable ranges (inclusive), as of October 20, 2026

| PHP branch | Vulnerable range |
| --- | --- |
| 5.4 | 5.4.0 to 5.4.45 |
| 5.5 | 5.5.0 to 5.5.38 |
| 5.6 | 5.6.0 to 5.6.40 |
| 7.0 | 7.0.0 to 7.0.33 |
| 7.1 | 7.1.0 to 7.1.33 |
| 7.2 | 7.2.0 to 7.2.34 |
| 7.3 | 7.3.0 to 7.3.33 |
| 7.4 | 7.4.0 to 7.4.33 |
| 8.0 | 8.0.0 to 8.0.30 |
| 8.1 | 8.1.0 to 8.1.34 |
| 8.2 | 8.2.0 to 8.2.34 |
| 8.3 | 8.3.0 to 8.3.35 |
| 8.4 | 8.4.0 to 8.4.26 |
| 8.5 | 8.5.0 to 8.5.11 |

Treat unlisted branches as `unknown` until an advisory confirms their status. Prerelease builds (dev/alpha/beta/rc) above the cutoff are also `unknown`.

## Notes

- The running PHP build and the vendor package advisory are the evidence. Configuration settings alone do not show PHP is patched.
- Never run exploit probes.
