# tinycast-extension

[Tinycast](https://github.com/abue-ammar/tinycast) extensions maintained by [@hlouis](https://github.com/hlouis). They use the Raycast extension format, but Tinycast is the only host they are developed and tested against.

## Extensions

| Folder | Extension | Upstream |
| --- | --- | --- |
| [`extensions/gitlab`](extensions/gitlab) | GitLab | [raycast/extensions](https://github.com/raycast/extensions/tree/main/extensions/gitlab) by tonka3000, MIT |

Each folder is a self-contained package: its own `package.json`, dependencies and lockfile. Nothing is shared across folders, and there is no workspace root.

## Installing into Tinycast

Settings → Extensions → **Install from GitHub**, with the extension folder's URL:

```
https://github.com/hlouis/tinycast-extension/tree/main/extensions/gitlab
```

Pick **npm** as the package manager; the lockfiles here are npm's. Tinycast downloads that folder over the anonymous GitHub API, runs the package manager and `ray build`, and keeps only the build — so this repository has to stay public, and Node plus npm have to be installed on the machine.

## Development

No Raycast involved. The loop is: edit, build, install the build, look at it in Tinycast.

```sh
cd extensions/gitlab
npm install                                  # once per clone
npx ray build -e dist -o ../../.build/gitlab # what Tinycast runs during an install
```

Then Settings → Extensions → **Add from folder** → `.build/gitlab`.

`ray build` compiles one JavaScript bundle per command declared in `package.json`. The build output is what Tinycast installs; the source folder itself is never installed.

There is no hot reload. `ray develop` only drives the Raycast app — it writes a build and then opens a `raycast://cli/…` deeplink, and Tinycast's deeplink router answers `raycast://extensions/…` only. After each change, rebuild and add the folder again.

`npx ray lint` is useful for ESLint and Prettier. Its `package.json` validation resolves `author` against the Raycast store's user registry, which has nothing to do with this repository, so that one error is expected.

### What the host supports

Tinycast reimplements `@raycast/api` natively, and the parts it leaves out fail in two different ways. `AI`, `BrowserExtension` and `WindowManagement` exist and reject with "not supported in Tinycast extensions yet". `OAuth` is absent entirely, so touching it is a `TypeError` — and a client constructed at module scope takes down every command that imports the module, which is what `extensions/gitlab/src/oauth.ts` works around. Build such objects on first use.

Commands run as `view`, `no-view` or `menu-bar`; a `menu-bar` or `no-view` command re-runs on its manifest `interval`. The runtime also provides a subset of Node builtins (`fs`, `child_process`, `crypto`, `http`/`https`, `net`, `zlib`, streams, …). npm dependencies are bundled by `ray build`, so pure-JavaScript packages work and native addons do not.

### Preferences survive nothing

Uninstalling an extension deletes everything it stored, preferences included — tokens and instance URLs have to be set again. They live in `~/Library/Application Support/com.tinycast.app/extension-data/<extension>.json`.

To carry them across a reinstall, copy that file aside first. Restoring it only sticks while Tinycast is not running: a running app holds the preferences in memory and writes them back over the file.

## Changes against upstream

### gitlab

- The OAuth PKCE client is built on first use instead of at import, so the extension stays usable with a personal access token on a host without `OAuth`.
- The Issues menu bar can filter to overdue issues (`Overdue Only`), and issue entries show their due date.
