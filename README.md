# iceburg-ci-downstreamer

:gift: an always-updating frontend into iCEBURG CI, aka the "entrypoint installer".

upon execution
the downstreamer creates a unique thread safe workspace containing an updated copy of the [iCEBURG CI](https://github.com/iceburg-net/iceburg-ci) tooling and acts
as a frontend into it by passing off execution.

it can be added to your project repositories or installed as a system-wide tool.

## quickstart

As a pre-requisite, your project is expected to follow [iCEBURG CI](https://github.com/iceburg-net/iceburg-ci) conventions -- at minimum it should provide a `ci/docker-compose.yml` file definining step behavior.

### including in a project

Include the [bin/ci](bin/ci) file in your repository, making sure it is marked as **executable**. this method requires no preinstallation on developer machines.

```
$your-project$
mkdir -p bin
curl https://raw.githubusercontent.com/iceburg-net/iceburg-ci-downstreamer/main/bin/ci > \
 bin/ci && chmod +x bin/ci

```

> You may use a path other than `bin/ci`, just make sure the script properly detects PROJECT_ROOT (top level of your repository). the scrippt currently expects the project root  to be one directory up from its location. If your project is managed by git, you can use `PROJECT_ROOT="$(git rev-parse --show-toplevel)"`.

Once added, simply run `bin/ci` from within your project to kickoff CI step execution.


### as a system tool

Add [bin/iceburg-ci](bin/iceburg-ci) to a directory in your PATH, making sure it is marked as **executable**. this method assumes projects are `git` managed.

```
sudo curl https://raw.githubusercontent.com/iceburg-net/iceburg-ci-downstreamer/main/bin/iceburg-ci > \
 /usr/local/bin/iceburg-ci && sudo chmod +x /usr/local/bin/iceburg-ci

```

Once added, simply run `iceburg-ci` from within your project to kickoff CI step execution.

## environment variables

name | default | description
--- | --- | ---
ICEBURG_CI_BRANCH | ~empty | The branch of the iceburg-ci repository to use for the workspace. if empty, the default branch is used.
ICEBURG_CI_DOWNSTREAMER_URL | https://github.com/iceburg-net/iceburg-ci-downstreamer.git | URL of the iceburg-ci-downstreamer repository. Can be a path.
ICEBURG_CI_HOME | ~/.iceburg-ci | home for CI tooling workspaces.
ICEBURG_CI_MAX_AGE | 60 | Number of seconds allowed before checking for updates.
ICEBURG_CI_SKIP_CLEANUP | false | True to skip cleaning up workspaces and tmp files.
ICEBURG_CI_URL | https://github.com/iceburg-net/iceburg-ci.git | URL of shared iceburg-ci repository. Can be a path.
