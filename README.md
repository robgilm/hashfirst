# Hashfirst

A single-file browser client for the public [Have I Been Pwned](https://haveibeenpwned.com) API.
No API key, no account, no backend, no dependencies.

The name is the guarantee: your password is hashed **before** anything is sent.

## Use it now

**→ [robgilm.github.io/hashfirst](https://robgilm.github.io/hashfirst/)**

Nothing to install, download, sign up for, or configure. Open the link and it works.

It runs entirely in your browser — there is no server behind it that could log what you
type. The page is served as static files from GitHub Pages, and every API call goes
straight from your browser to Have I Been Pwned.

## What it does

| Tab | Endpoint | Key required |
|---|---|---|
| Check password | `api.pwnedpasswords.com/range/` | no |
| Breach details | `/api/v3/breach/{name}` | no |
| All breaches | `/api/v3/breaches` | no |

Email search is deliberately absent. That endpoint (`/api/v3/breachedaccount/`) requires a
paid HIBP subscription and would mean sending your address to a server. Use
[haveibeenpwned.com](https://haveibeenpwned.com) directly for that — it's free in the browser.

## How the password check stays private

1. You type a password. It never leaves the input field.
2. The browser computes its SHA-1 locally via `crypto.subtle`.
3. Only the **first 5 hex characters** of that hash are sent to the range API.
4. The API returns every hash suffix sharing that prefix — hundreds of them.
5. Your suffix is matched against that list **in your browser**.

HIBP therefore never learns which password you checked. This is the
[k-anonymity model](https://blog.cloudflare.com/validating-leaked-passwords-with-k-anonymity/).
Requests also set `Add-Padding: true`, so the response is a uniform size and its length
leaks nothing either.

## Running your own copy

Optional — the hosted link above needs none of this. But it's one file, so self-hosting is
trivial if you'd rather not trust someone else's deployment.

Serve it over `http://localhost` or HTTPS. It needs a secure context, because
`crypto.subtle` is unavailable over `file://`:

```bash
python -m http.server 8899 --bind 127.0.0.1
```

Then open <http://127.0.0.1:8899>. Any static host works — it's one file with no build step.

If you open the file directly from disk, the page tells you so rather than failing silently.

## Attribution

Breach data is sourced from [Have I Been Pwned](https://haveibeenpwned.com), created by
[Troy Hunt](https://troyhunt.com), and is licensed under a
[Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).
The Pwned Passwords API carries no attribution requirement, but credit is given anyway.

This project is not affiliated with, endorsed by, or operated by Have I Been Pwned.

Inspired by — but sharing no code with — the
[PwnyTrap CLI](https://github.com/davewatters/pwnytrap) by David Watters.

## License

[MIT](LICENSE) for this project's own code. The HIBP data it displays remains CC BY 4.0.
