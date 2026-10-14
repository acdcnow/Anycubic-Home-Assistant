# Changelog

All notable changes to this integration. The version numbers follow upstream
[Nino6689/hass-anycubic](https://github.com/Nino6689/hass-anycubic), which this
fork tracks: anything before 2.9.6 is upstream's, and is recorded in
[upstream's releases](https://github.com/Nino6689/hass-anycubic/releases).

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and
this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.9.6] - 2026-10-14

### Fixed

- **The card no longer fails with `already been used with this registry` when a
  second copy of it is loaded.** Every element the card and the panel register now
  goes through the duplicate-safe `customElementIfUndef` decorator, which checks
  `customElements.get()` first. `anycubic-printercard-configure` was the last one
  still using lit's stock `@customElement`, whose bare `customElements.define()`
  throws as soon as the name is taken — which it is when the abandoned standalone
  card (`WaresWichall/hass-anycubic_card`) is installed beside this integration.
  Home Assistant 2026.10 is where this became visible, because its scoped custom
  element registry enforces one definition per name and reports the collision
  instead of ignoring it. With the remaining nine registrations converted, neither
  copy can trip over the other whichever loads first.

### Changed

- **The frontend build works on Windows.** `npm run build` and `npm run build_card`
  failed at the babel step, which was invoked as `./node_modules/.bin/babel` — a
  Unix-only path. Both now call `babel`, which npm resolves from
  `node_modules/.bin` on every platform, as the `eslint` script already did.

### Added

- **A troubleshooting entry for the duplicate-definition error**, naming the old
  standalone card as the usual second copy and listing both places to remove it:
  the HACS **Frontend** entry and its dashboard resource. The
  [Moving from v0.2.2](README.md#-moving-from-v022) steps now include that removal
  too, since the card has shipped inside the integration since 2.x.

### Notes

- `frontend_panel/dist/` was rebuilt with the repository's own pipeline
  (`npm run build`, `npm run build_card`).
- **The card reaches Home Assistant through the separate `anycubic-cloud-frontend`
  PyPI package, not from this repository.** This `dist/` has to be copied there,
  with that package's content hash and version bumped, before the guard reaches a
  real install. Until that happens the released card keeps its old behaviour, and
  removing the standalone card remains the fix in Home Assistant.
