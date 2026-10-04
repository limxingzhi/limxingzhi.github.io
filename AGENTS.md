# limxingzhi.github.io

Agent guide for this repo. The root `index.html` redirects to https://xingzhi.dev/blog/about.

Small web apps are bundled here as git submodules. Each app lives in its own repository, checked out into a folder and recorded in this repo at one exact commit. This file covers the layout and the workflow for changing those apps.

## Layout

| Path | Repository |
| --- | --- |
| `pomodoro/` | [pomodoro-timer](https://github.com/limxingzhi/pomodoro-timer) |
| `vue-name-gen/` | [vue-name-generator](https://github.com/limxingzhi/vue-name-generator) |
| `sb-text/` | [sb-text](https://github.com/limxingzhi/sb-text) |
| `markdown/` | [markdown-viewer](https://github.com/limxingzhi/markdown-viewer) |
| `multi-video/` | [multi-video-watcher](https://github.com/limxingzhi/multi-video-watcher) |
| `index.html` | Redirect page, tracked in this repo |
| `.gitmodules` | Submodule paths and URLs |

`.gitmodules` also lists a `journal` submodule that no commit tracks. Ignore it.

Populate the submodules after cloning:

```
git submodule update --init
```

## Changing a submodule

The parent repo records one commit per submodule. Changing an app means committing and pushing in the app's own repo, then committing the new pointer here.

### 1. Check out the submodule branch

`git submodule update --init` leaves the submodule on a detached HEAD at the recorded commit. Check out its default branch before editing. These repos use `master`, not `main`.

```
git submodule update --init multi-video
cd multi-video
git checkout master
```

### 2. Edit and test

Change the app, run it locally, and confirm the behavior you want.

### 3. Commit and push the submodule

`.gitmodules` records HTTPS URLs. Pushing over HTTPS needs stored credentials, which are not set up here, so push over SSH to the same repository.

```
git add -A
git commit
git push git@github.com:limxingzhi/multi-video-watcher.git master
```

Then fetch, so the submodule's `origin/master` tracking ref moves to the commit you just pushed:

```
git fetch origin master
```

### 4. Update the parent repo

Stage the new submodule commit, commit the pointer, and push.

```
cd ..
git add multi-video
git commit
git push origin master
```

## Pitfalls

`git submodule update --init` leaves a detached HEAD. Check out `master` before committing anything.

`.gitmodules` uses HTTPS URLs, which cannot push without credentials. Push over SSH instead.

The git config sets `push.recursesubmodules=check`. Before a push, git checks that every submodule commit exists on a submodule remote. A push made through an explicit SSH URL does not update the submodule's `origin/master` tracking ref, so the check fails and the parent push aborts:

```
The following submodule paths contain changes that can not be found on any remote:
  multi-video
```

The `git fetch origin master` in step 3 moves `origin/master` to the pushed commit, and the parent push then succeeds.
