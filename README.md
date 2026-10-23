# PHP Version Checker (Claude Code)

Claude Code skills that check a website or local PHP binary for php2shell
exposure. Port of [openai/php2shell-checker](https://github.com/openai/php2shell-checker)
(Apache-2.0).

## Install

As a plugin from GitHub (this repo is its own marketplace):

```text
/plugin marketplace add ramzieus/php2shell-checker-claude
/plugin install php2shell-checker@php2shell-checker
```

Or load a local clone for one session:

```sh
claude --plugin-dir /path/to/php2shell-checker-claude
```

Or copy the skills for personal use (copy both; `phpcheck-server` reuses the
checker bundled in `phpcheck-url`):

```sh
cp -r skills/* ~/.claude/skills/
```

Requires Node.js 18+.

## Use

Only have a URL — `phpcheck-url`:

```text
/php2shell-checker:phpcheck-url https://example.com
```

Have server access — `phpcheck-server`:

```text
/php2shell-checker:phpcheck-server
```

Claude also invokes them automatically when you ask, e.g. "is example.com's PHP
vulnerable to php2shell?". When copied to `~/.claude/skills/`, drop the
`php2shell-checker:` prefix.

## Run directly

```sh
node phpcheck.js "https://example.com"
node phpcheck.js --local
```

## Results

| Result | Exit code | Meaning |
| --- | --- | --- |
| `OK` | `0` | Stable PHP patch version above the embedded cutoff for a listed branch. |
| `VULNERABLE` | `1` | PHP version at or below the embedded cutoff for that branch. |
| `UNKNOWN` | `3` | No PHP version or response, an unrecognized branch, or a newer prerelease build. |

`--json` gives machine-readable output (`patch_status` is `ok`, `vulnerable`,
or `unknown`). Invalid arguments exit with code `2`.

## Tests

```sh
node --test phpcheck.test.js
```

Tests use local HTTP servers and a fake PHP executable. No PHP install or
external site needed.

## Limitations

Only versions exposed in `X-Powered-By` / `Server` headers or `php -v` are
evaluated. Sends `HEAD`, falls back to `GET` on 405/501, no redirects, no body
inspection. Hidden headers return `UNKNOWN`.

`OK` is a version comparison assuming later stable patches in a branch contain
the fix. It does not verify a fix independently; confirm against official PHP
security releases. Headers can be hidden or changed, and distribution backports
may not change the upstream version. `UNKNOWN` does not mean patched.

## License

[Apache License 2.0](LICENSE).
