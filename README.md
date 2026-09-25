# Nook — built web app

Generated output only. **No source lives here.**

This repository exists solely to serve the Nook web build over GitHub Pages, because the source
repository is private and GitHub Pages requires a public repository on a free plan. Everything
under `docs/` is written by the source repository's deploy script and replaced wholesale on each
deploy — edits made there will be lost.

Source: the private `dancki/NookDev` repository.

## How a deploy happens

From a `dancki/NookDev` checkout:

```sh
sh scripts/deploy-web.sh /path/to/this/checkout
```

The script builds the app with `expo export --platform web` for the `/Nook` base path, with
`https://dancki.github.io/Nook` as the origin for the links the app generates. It takes the
Supabase URL and publishable key from NookDev's `.env` when there is one (otherwise from the
environment), and refuses to build if the URL is empty or points at localhost, or if the key is
empty. The Turnstile site key (`EXPO_PUBLIC_TURNSTILE_SITE_KEY`, the sign-in bot check) comes
from the same place and is optional: unset builds with captcha off, and the script prints
`captcha=on` or `captcha=off` so you can tell which one you shipped. It refuses Cloudflare's test
keys unless `ALLOW_TURNSTILE_TEST_KEY=1` is set, because their dummy tokens would lock out every
sign-in the moment captcha is switched on in Supabase. Switch captcha on in Supabase only after a
build with the real site key is live here. It then deletes `docs/` here, copies the export in, re-creates the two scaffolding files
below, and prints `git status` for this repository. Committing and pushing here is done by hand;
the push is what Pages publishes.

Pass the path explicitly: without an argument the script looks for `../nook` beside the NookDev
checkout, lowercase, which misses a checkout named `Nook` on a case-sensitive filesystem.

GitHub Pages cannot be configured to send custom response headers, so the build is served with
only the Content Security Policy its `index.html` carries as a meta tag, and without clickjacking
protection (`frame-ancestors` works only as a header). That makes this a preview host, not the
production one; NookDev's `docs/ops/production-checklist.md` §5 lists the headers a production
host must send.

## Why the build lives in `docs/`

GitHub Pages for this repository is configured to publish from the **`main` branch, `/docs`
folder**. The first deploy put the build at the repository root instead, so the Pages job ran
Jekyll against a `docs/` directory that did not exist, failed with
`No such file or directory @ dir_chdir0 - /github/workspace/docs`, skipped its deploy step, and
`dancki.github.io/Nook/` answered "Site not found".

`docs/` is only the *source* directory — its contents are still served from the site root, so the
build's `/Nook` base path is unaffected by this layout. **If the Pages source is ever switched to
`/ (root)`, this directory must move back up**, or the site breaks the same way in reverse.

## Two files that are scaffolding, not build output

The deploy script re-creates both on every deploy, since it replaces `docs/` wholesale.

- `docs/.nojekyll` — GitHub Pages runs Jekyll by default, and Jekyll ignores any directory whose
  name begins with an underscore. Without this file the entire `_expo/` directory (which holds
  the JavaScript bundle) is silently dropped and the site loads a blank page. It has to sit
  inside the published directory, not at the repository root.
- `docs/404.html` — a byte-for-byte copy of `index.html`. Pages serves static files, so a deep
  link such as `/Nook/callback` has no file behind it and would 404; serving the app shell from
  the 404 page hands the route to the client-side router instead. This is what makes magic-link
  sign-in work on the web.
