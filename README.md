# iceburg-ci-downstreamer

:gift: an always-updating frontend into iCEBURG CI, aka the "entrypoint installer".

the downstreamer provides a thread safe workspace containing an updated checkout of [iCEBURG CI](https://github.com/iceburgci/iceburg-ci) and passes of execution to it.

it can be added to individual project repositories or installed as a system-wide tool.

## project setup

Your project is expected to follow [iCEBURG CI](https://github.com/iceburgci/iceburg-ci) conventions -- at minimum it must;
 * provide the "bin/ci" tooling for running steps, either locally in the project, or as a system tool.
 * provide a `ci/docker-compose.yml` file defining step behavior. See iCEBURG CI [TBD examples]().

### including in a project

Include the [bin/ci](bin/ci) file in your repository, making sure it is marked as **executable**. this method requires no preinstallation on developer machines and offers visibility into what is executed.

```
$your-project$
mkdir -p bin
curl https://raw.githubusercontent.com/iceburgci/iceburg-ci-downstreamer/main/bin/ci > \
 bin/ci && chmod +x bin/ci

```

> You may use a path other than `bin/ci`, just make sure the script properly detects PROJECT_HOME (top level of your repository). the scrippt currently expects the project root  to be one directory up from its location. If your project is managed by git, you can use `PROJECT_HOME="$(git rev-parse --show-toplevel)"`.

:rocket: Once added, simply run `bin/ci` from within your project to kickoff CI step execution.


### as a system tool

Add [bin/iceburg-ci](bin/iceburg-ci) to a directory in your PATH, making sure it is marked as **executable**. this method assumes projects are `git` managed.

```
sudo curl https://raw.githubusercontent.com/iceburgci/iceburg-ci-downstreamer/main/bin/iceburg-ci > \
 /usr/local/bin/iceburg-ci && sudo chmod +x /usr/local/bin/iceburg-ci

```

:rocket: nce added, simply run `iceburg-ci` from within any directory of your project to kickoff CI step execution.

## environment variables

environment variables are used to customize downstreamer behavior, such as where workspaces are created or the repository used for cloning a private/custom version of iCEBURG CI.


name | default | description
--- | --- | ---
ICEBURG_CI_BRANCH | ~empty | The branch of the iceburg-ci repository to use for the workspace. if empty, the default branch is used.
ICEBURG_CI_DOWNSTREAMER_URL | https://github.com/iceburgci/iceburg-ci-downstreamer.git | URL of the iceburg-ci-downstreamer repository. Can be a path.
ICEBURG_CI_HOME | ~/.iceburg-ci | home for CI tooling workspaces.
ICEBURG_CI_MAX_AGE | 60 | Number of seconds allowed before checking for updates.
ICEBURG_CI_SKIP_CLEANUP | false | True to skip cleaning up workspaces and tmp files.
ICEBURG_CI_URL | https://github.com/iceburgci/iceburg-ci.git | URL of shared iceburg-ci repository. Can be a path.


### self hosted iceburg CI

it may be preferable to host your own downstreamer and iCEBURG CI repository for your organization. this will allow you to share custom steps and tooling with your projects in a safe way.  

you are able to control to control which repositories are used with the `ICEBURG_CI_DOWNSTREAMER_URL` and `ICEBURG_CI_URL` variables. you can hardcode values by modifying the downstreamer script or provide them via the environment on CI platforms and developer machines accordingly.


#### GitHub Actions

The [iCEBURG CI action (TBD)]() for configuring the environment supports setting the CI repositories used via the `TBD` parameters;

#### GitLab CI

The `CI_BUILD_TOKEN` can be leveraged for cloning repositories as the executing user in GitLab. If your iCEBURG CI fork is named `ci/acme-ci.git`, A relatively portable approach is to set `ICEBURG_CI_URL` as a CI variable:

name | value
--- | ---
ICEBURG_CI_URL | $CI_SERVER_PROTOCOL://gitlab-ci-token:$CI_BUILD_TOKEN@$CI_SERVER_HOST:$CI_SERVER_PORT/ci/acme-ci.git

or inside a pipeline template;

```yml

variables:
  ICEBURG_CI_URL: $CI_SERVER_PROTOCOL://gitlab-ci-token:$CI_BUILD_TOKEN@$CI_SERVER_HOST:$CI_SERVER_PORT/ci/acme-ci.git
  ...

```
