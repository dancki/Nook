# Nook — built web app

Generated output only. **No source lives here.**

This repository exists solely to serve the FamilyManager web build over GitHub Pages, because
the source repository is private and GitHub Pages requires a public repository on a free plan.
Every file here is produced by `expo export --platform web` and is overwritten wholesale on each
deploy — edits made here will be lost.

Source: the private `dancki/FamilyManager` repository.

Two files are deployment scaffolding rather than build output:

- `.nojekyll` — GitHub Pages runs Jekyll by default, and Jekyll ignores any directory whose name
  begins with an underscore. Without this file the entire `_expo/` directory (which holds the
  JavaScript bundle) is silently dropped and the site loads a blank page.
- `404.html` — a byte-for-byte copy of `index.html`. Pages serves static files, so a deep link
  such as `/Nook/callback` has no file behind it and would 404; serving the app shell from the
  404 page hands the route to the client-side router instead. This is what makes magic-link
  sign-in work on the web.
