# legacy-stubs — NOT part of this site

These files are **staging only**. They are never served from this repo, and
GitHub Pages ignores them for our purposes (nothing links to them). They are
here so the redirect pages live in version control until they are needed.

## What they are

Tiny pages that forward the **old** URLs

```
https://flushatoilet.github.io/openhoops-site/<page>.html
```

to the **new** home

```
https://tim-builds.dev/openhoops/<page>.html
```

One stub per old page: `index.html`, `privacy.html`, `terms.html`,
`delete-account.html`, `reset.html`, `court.html`.
(`index.html` forwards to `https://tim-builds.dev/openhoops/`.)

## Why JavaScript and not a meta refresh

`reset.html` is the landing page for Supabase password-reset emails. Supabase
puts the short-lived recovery token in the **URL fragment**
(`#access_token=...`), and a `<meta http-equiv="refresh">` **drops the
fragment** — the user would arrive at the new page with no token and be told
the link is invalid.

So every stub uses:

```js
location.replace(NEW_URL + location.search + location.hash);
```

which carries both the query string and the fragment across. A `<noscript>`
plain link is the fallback (it loses the fragment, but it is better than a
dead end, and it only matters for users with JS disabled — who could not use
the reset page anyway).

## How they get deployed — AFTER the transfer

1. `openhoops-site` (this repo) is transferred to the **tim-builds** org and
   gets the custom domain `tim-builds.dev`. At that moment GitHub redirects
   the old `flushatoilet.github.io/openhoops-site/` URLs to the new location
   — but that courtesy redirect is not something to rely on long-term, and it
   disappears if the old repo name is reused.
2. **Recreate** an empty repo named `openhoops-site` under the personal
   account `flushatoilet`.
3. Copy the `.html` files from this directory into the root of that new repo
   (do **not** copy this README, and do **not** add a `CNAME` file there).
4. Enable GitHub Pages on it (deploy from `main`, root).
5. Verify: open the old reset URL with a fake fragment, e.g.
   `https://flushatoilet.github.io/openhoops-site/reset.html#access_token=test`
   and confirm you land on
   `https://tim-builds.dev/openhoops/reset.html#access_token=test`
   with the fragment intact.

## Also remember

The Supabase Auth **redirect URL / site URL** settings still point at the old
GitHub Pages address. Update them to
`https://tim-builds.dev/openhoops/reset.html` so new emails go straight to the
new page; these stubs only cover emails already sent.
