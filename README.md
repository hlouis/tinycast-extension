# tinycast-extension

[Tinycast](https://github.com/abue-ammar/tinycast) extensions maintained by [@hlouis](https://github.com/hlouis). They are ordinary Raycast extensions, so they also build and run under Raycast.

## Extensions

| Folder | Extension | Upstream |
| --- | --- | --- |
| [`extensions/gitlab`](extensions/gitlab) | GitLab | [raycast/extensions](https://github.com/raycast/extensions/tree/main/extensions/gitlab) by tonka3000, MIT |

## Installing into Tinycast

Settings → Extensions → **Install from GitHub**, and paste the extension's folder URL:

```
https://github.com/hlouis/tinycast-extension/tree/main/extensions/gitlab
```

Tinycast downloads that folder, runs the package manager and `ray build`, and keeps only the build. Node and one of npm/pnpm/Yarn/Bun must be installed; pick npm in that dialog, since the lockfiles here are npm's.

## Changes against upstream

### gitlab

- The OAuth PKCE client is built on first use instead of at import. Tinycast's `@raycast/api` has no `OAuth`, so constructing it at import killed every command, including the menu bars, even with a personal access token configured.
- The Issues menu bar can filter to overdue issues (`Overdue Only`), and issue entries show their due date.

## Working on an extension

```sh
cd extensions/gitlab
npm install
npx ray build -e dist   # what Tinycast runs
npx ray develop         # live reload, needs Raycast
```
