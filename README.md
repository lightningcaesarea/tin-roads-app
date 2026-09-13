# Tin Roads — app build

The installable build of [Tin Roads](https://github.com/lightningcaesarea/tin-roads),
an empire-building sandbox on the Iron Age Mediterranean.

**Play / install:** https://lightningcaesarea.github.io/tin-roads-app/

This repo is public only so GitHub Pages will serve it. The project's source of
truth — including the sourced Late Bronze Age research the game is built on —
lives in the private `tin-roads` repo.

---

## Installing on a phone

There is no app store listing; it installs straight from the browser and then
behaves like any other app — own icon, own window, works offline.

**iPhone / iPad.** Open the link in **Safari** (this does not work from Chrome
on iOS), tap Share, then *Add to Home Screen*.

**Android.** Open the link in Chrome. It should offer *Install app*; if not,
use the ⋮ menu → *Add to Home screen*.

Once installed it runs offline. Saves live in the browser's storage for the
installed app, and the app asks the system to treat that storage as persistent.
Deleting the app deletes its saves, so use the game's own text export for
anything you want to keep.

---

## Updating it

Publish a new build from the private repo:

```powershell
.\publish.ps1 -Push
```

That picks the highest-versioned `tin_roads_empire_v*.html` out of the private
repo, regenerates `index.html` and `sw.js`, commits and pushes. Pages redeploys
within a minute, and installed copies pick the new build up the next time they
are opened with a connection — no reinstall.

To publish a specific build, or to generate without pushing:

```powershell
.\publish.ps1 -Source "..\Tin Roads\tin_roads_empire_v0.15.0.html"
```

---

## What is generated, and what is not

| File | |
|---|---|
| `index.html` | **Generated.** The game with a PWA shell injected into its `<head>`. |
| `sw.js` | **Generated.** Service worker, cache keyed to the build's version. |
| `manifest.webmanifest` | Hand-maintained. Name, icons, colours, display mode. |
| `icons/` | Hand-maintained. Oxhide ingot in the game's ochre on its abyss blue. |
| `publish.ps1` | Hand-maintained. The generator. |

Never edit `index.html` or `sw.js` directly — edit the build in the private
repo and re-publish, or the next publish silently discards the change.

## How updating actually works

The service worker serves the page **network-first**, so a new build lands as
soon as the device has a connection rather than waiting for a cache to expire;
the cache is only the offline fallback. Everything else is cached by a key that
includes the version, and old caches are deleted on activation, so a publish
cannot leave a half-old, half-new app behind.

## Known limitations

- Service worker registration could not be verified on this machine — the
  development browser blocks it. It needs a check on a real device: install,
  turn off the network, and confirm the game still opens.
- Page zoom is pinned off, because it fought the map's own pinch-zoom.
- The iOS status bar is opaque rather than translucent. The game's layout is a
  fixed `100vh` grid, so letting content run under a notch would break it.
  Going full-bleed would mean teaching the layout about `env(safe-area-inset-*)`.
