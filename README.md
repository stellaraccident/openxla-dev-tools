# OpenXLA Development Tools

This repository contains a Python package providing development tools
that span the OpenXLA suite of projects under the [openxla org](https://github.com/openxla).

It is primarily intended to be installed by developers. In everyday use,
it should not be necessary to actual modify the tools themselves.

## Installing the tools

```
pip install git+https://github.com/stellaraccident/openxla-dev-tools.git
```

## openxla-workspace User's Guide

The `openxla-workspace` command is used to:

* Setup and maintain an "OpenXLA workspace" (collection of individual project
  repositories).
* Checkout individual project repositories.
* Sync repositories to pinned versions.
* Update pinned version.

Quick start to create an openxla repository and start developing:

```
mkdir ~/openxla
cd ~/openxla
openxla-workspace init
openxla-workspace checkout openxla-pjrt-plugin
```

The above will initialize a new workspace and checkout the `openxla-pjrt-plugin`
plus its dependencies, syncing to the dependency version pins that the project
specifies. There is nothing special about this project except that it is a
leaf project and illustrates dependency resolution.

### Updating version pins

NOTE: Some projects manage their dependencies via submodules. Such cases are
not covered here (just use normal `git` tools).

If a project depends on other OpenXLA projects, it expresses these dependencies
via `sync_deps.py` script in the root of its repository. This script is both
importable (in order to get programmatic access to dependencies) and runnable
(so that end-developers and CI systems have the simplest possible mechanism
to check everything out needed to build the project).

When it is time to update the pins in this file, bring the dependencies of
interest to the desired commit (and land it in the corresponding dependency
repo). Then, from the project to be updated, run `openxla-workspace pin`.

Example:

```
cd ~/openxla/openxla-pjrt-plugin
openxla-workspace pin --require-upstream
```

Note that the `--require-upstream` flag enables an (expensive) safety check
to ensure that the chosen commits are on the appropriate upstream tracking
branch.

This will overwrite the `sync_deps.py` script with a new version. Creating a
PR in the repo with changes to this file will trigger the CI to validate the
chosen commits.

This same basic procedure will be applied by automation which periodically
moves all dependencies forward when there are no build/test issues.