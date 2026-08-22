# Keeping the auxiliary apps up to date

This fork serves a restricted app set: `rareBit` plus seven apps maintained
upstream in [espruino/BangleApps](https://github.com/espruino/BangleApps) —
`boot`, `antonclk`, `setting`, `widlock`, `widid`, `dtlaunch`, `widbt`.

`bin/sync-upstream-apps.mjs` keeps those seven in step with upstream without
merging the whole repository.

## Usage

```sh
node bin/sync-upstream-apps.mjs                        # report only
node bin/sync-upstream-apps.mjs --pull                 # report, then update stale apps
node bin/sync-upstream-apps.mjs --pull --app setting   # limit to named apps
node bin/sync-upstream-apps.mjs --pull --all           # refresh everything, ignore versions
```

Run it **before each release**; the cadence is manual on purpose (see
_Deferred_ below).

After a `--pull`, always run the repo checks before committing:

```sh
node bin/sanitycheck.js
npm run lint-apps && npm run lint-modules
```

## How it works

- The served app list comes from the `restricted:` array in `apps.json`, minus
  `rareBit` — the fork's own app is never touched by the script.
- Each app's version is compared against upstream `master`.
- Files are fetched from `raw.githubusercontent.com`, so no GitHub token, API
  quota, or full clone is needed. The file list for an app is derived from that
  app's upstream `metadata.json` (`storage`, `data`, `icon`, `readme`,
  `screenshots`, `custom`, `customConnect`, `interface`) plus its `ChangeLog`.
  Missing optional files (ChangeLog, screenshots) are skipped, not an error.
- Any checked-in `<appid>.info` is removed — the loader generates those.
- **Modules are synced too.** This fork's `modules/` predates current upstream,
  and a refreshed app that `require()`s a stale module will break on device. So
  after pulling apps, the script scans the updated app sources for `require()`
  calls (line comments stripped) and pulls each matching `modules/*.js` from
  upstream. Names with no `modules/` file upstream — firmware builtins like
  `Storage` or `heatshrink`, or app-provided modules — are reported and skipped.

## Caveats

- Upstream apps can start using metadata keys or device IDs that this fork's
  `bin/sanitycheck.js` doesn't know yet (this happened with `author`,
  `requires_firmware`, `BANGLEJS3` and `BANGLEJS3_COMPAT`). If sanitycheck
  errors on freshly pulled metadata, refresh `bin/sanitycheck.js` from upstream
  and re-apply the fork's local tweak (it skips the locale checks, since the
  restricted set has no `locale` app).
- An upstream app may pick up a dependency on an app this loader doesn't serve.
  Nothing pulled so far does, but check the `dependencies` field after a pull.

## Deferred

A scheduled GitHub Action that runs the report and opens an auto-PR was
considered and deferred — it needs workflow permissions and a bot token that
this fork doesn't currently have set up. Run the script manually for now.
