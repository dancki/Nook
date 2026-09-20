# Nook — built web app

Generated output only. **No source lives here.**

This repository exists solely to serve the FamilyManager web build over GitHub Pages, because
the source repository is private and GitHub Pages requires a public repository on a free plan.
Every file under `docs/` is produced by `expo export --platform web` and is overwritten wholesale
on each deploy — edits made there will be lost.

Source: the private `dancki/FamilyManager` repository.

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

- `docs/.nojekyll` — GitHub Pages runs Jekyll by default, and Jekyll ignores any directory whose
  name begins with an underscore. Without this file the entire `_expo/` directory (which holds
  the JavaScript bundle) is silently dropped and the site loads a blank page. It has to sit
  inside the published directory, not at the repository root.
- `docs/404.html` — a byte-for-byte copy of `index.html`. Pages serves static files, so a deep
  link such as `/Nook/callback` has no file behind it and would 404; serving the app shell from
  the 404 page hands the route to the client-side router instead. This is what makes magic-link
  sign-in work on the web.
