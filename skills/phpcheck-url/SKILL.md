---
name: phpcheck-url
description: Check whether a website is running a php2shell-vulnerable PHP version, using only its URL or hostname. Use when the user gives a URL/domain and asks if the site's PHP is patched or vulnerable to php2shell, and has no server access.
argument-hint: <url-or-hostname>
allowed-tools: Bash(node *phpcheck.js*)
---

Check the PHP version advertised by `$ARGUMENTS`.

## Steps

1. Normalize the target. A bare hostname becomes `https://<hostname>`.
2. Run the checker:

   ```sh
   node "${CLAUDE_SKILL_DIR}/scripts/phpcheck.js" "<url>" --json
   ```

3. Read the JSON: `patch_status` (`ok` | `vulnerable` | `unknown`), `php_version`, `reason`, `exit_code`.
4. Report to the user: the version found, the patch status, and the update required if vulnerable.

## Exit codes

| Code | Meaning |
| --- | --- |
| `0` | `ok` |
| `1` | `vulnerable` (result, not a failure) |
| `3` | `unknown` (result, not a failure) |
| `2` | Bad arguments (real failure; fix the command) |

Do not treat 1 or 3 as execution errors.

## Interpreting results

- The checker reads only the `Server` and `X-Powered-By` headers. It sends `HEAD`, falls back to `GET` on 405/501, and does not follow redirects. If the version is hidden, the result is `unknown`. `unknown` does not mean patched.
- `ok` is a version comparison only. It assumes later stable patches in a listed branch contain the fix.
- Headers can be spoofed or stripped, and vendor backports can fix an older version string. Recommend a server-side check (`phpcheck-server`) for stronger evidence.
- Compare the reported version with current PHP security advisories before declaring the site safe.

## Boundaries

Never run exploit probes or send payloads. Only read headers via the script.
