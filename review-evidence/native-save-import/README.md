# Native Mac save import — review material

Reference material for [scope discussion #2420](https://github.com/seedds/nxnative/issues/2420), not a pull request. **This branch contains patches and evidence; it does not apply the feature to the runtime source at its root.** The prepared source is based on `224ecb186960febfc361071bc1c3f181e20dbefb`, not current `main` or an accepted menu prerequisite.

## Code

- [01-menu-prerequisite.patch](01-menu-prerequisite.patch): adaptation of [James Sturges's PR #921](https://github.com/seedds/nxnative/pull/921), head `f2401b4be6bed3ab231117bd5117d3f65a5b7d6e`, with its contribution and co-author attribution recorded. This is not a competing menu PR.
- [02-native-save-import.patch](02-native-save-import.patch): the importer-only diff, separate from that prerequisite.

Applying both patches to the prepared base reproduces tested source tree `949544ff703170915cb71843ac1be724c33bad48`. The branch leaves workflow files unchanged; it publishes the source patches rather than requesting permission to push the older prepared workflow history.

| Importer-only change | Added lines | Removed lines |
| --- | ---: | ---: |
| Implementation, excluding tests/docs/vendor headers | 999 | 60 |
| Complete source patch | 4,283 | 95 |

The complete patch includes 1,929 lines of unmodified public libarchive headers. The implementation count does not put the complete contribution below the scope guideline.

## Reproduce the source and focused checks

From a checkout of this review branch, with the project's Mac build dependencies installed:

```sh
root="$(git rev-parse --show-toplevel)"
git fetch https://github.com/seedds/nxnative.git 224ecb186960febfc361071bc1c3f181e20dbefb
git worktree add --detach "$root/../nxnative-import-check" 224ecb186960febfc361071bc1c3f181e20dbefb
git -C "$root/../nxnative-import-check" apply "$root/review-evidence/native-save-import/01-menu-prerequisite.patch"
git -C "$root/../nxnative-import-check" apply "$root/review-evidence/native-save-import/02-native-save-import.patch"
cd "$root/../nxnative-import-check"
make -C dm/host/tests GAME=010015100B514000 mac-menus
python3 -m unittest dm.tools.tests.test_nsp_mac_app.App
python3 dm/tools/docs_symptoms.py
python3 -m unittest dm.tools.tests.test_docs
```

Observed on an Apple M4 Max, macOS 27.2.1, Xcode 27.2:

- Native import/backup/restore/menu checks passed; final native compilation had no warnings.
- Mac app component suite: 26 passed. Documentation suite: 6 passed.
- Metadata cases cover binary ZIP data, selected roots, wrong title/type/revision, 85/86/87-byte boundaries, metadata-only inputs, ordinary/nested names, raw import-time dates and retained full-backup dates.

## Limits

- Console-exported JKSV backups, native Accept-click interaction and gameplay after loading were not exercised.
- Recognized device-save metadata is refused rather than routed to a guessed mount.
- No new full host/iOS build or five-game runtime regression sweep is claimed for this compatibility change.

No game files, save payloads, keys, extracted modules or shaders are included in this directory. Source patches and review notes only.
