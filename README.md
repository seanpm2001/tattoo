# Tattoo
Test all the things over and over.

## Installation

```
npm install -g tattoo
```

### Usage

When you run tattoo, it will create and/or reuse a directory called
`./tattoo_workspace`, relative to the current working dir.  This folder is
used to clone test target repositories and their dependencies.  You can change
the directory used with the `--workspace-dir` or `-w` option.

***Test one repo***
```
tattoo PolymerElements/paper-input
```
This is the simplest possible case.  What actually happens here is that tattoo
clones the repository `https://github.com/PolymerElements/paper-input` into the
local filesystem as `./tattoo_workspace/paper-input`, installs all of the
`dependencies` and `devDependencies` in its `bower.json` also into
`./tattoo_workspace`.  Then it essentially runs
`cd ./tattoo_workspace/paper-input && wct --local chrome`.

***Test two repos***

```
tattoo PolymerElements/paper-input PolymerElements/paper-icon-button
```
In this case, tattoo clones both repositories into `./tattoo_workspace` as
`paper-input` and `paper-icon-button`, then installs all of their combined
`dependencies` and `devDependencies` into `./tattoo_workspace`.  Conflicts in
dependencies are currently resolved arbitrarily, but there's an
[issue (#24)](https://github.com/Polymer/tattoo/issues/24) to address that.
It then runs `wct` for both custom element repos.

***Test all the paper repos***

```
tattoo PolymerElements/paper-*
```
Tattoo supports wildcards in repository references so the above actually clones
all the PolymerElements repos that start with `paper-`, installs their
dependencies and runs `wct` for all of the paper element repos.

***Test all the paper repos on a specific branch***

```
tattoo "PolymerElements/paper-*#2.0-preview"
```
In the previous examples, tattoo cloned the requested repositories and simply
ran tests on whatever HEAD of their repo is, (typically `master` branch).  It
is frequently the case that a specific branch, tag or commit/SHA1 reference is
the desired target for test.  Tattoo supports this with the `#hashref` syntax
so the above example `PolymerElements/paper-*#2.0-preview` would tell tattoo
to run tests for all the `paper-` elements which have a branch called
`2.0-preview`.  If the branch name is invalid for a given repo, it should be
skipped/excluded.

***Test all the repos except that one***

```
tattoo PolymerElements/* -s PolymerElements/style-guide
```

***Test repos with a config.***

Create a json config file with `branch-config` and/or `wctflags` keys:

`tattoo_config.json`:
```
{
  "test": [
    "PolymerElements/paper-button#2.0-preview"
  ],
  "wctflags": ["--local", "canary"]
}
```

Then run:

```
tattoo
```

Config files support most of the same options as the command-line flags:

* `"test": ["PolymerElements/paper-button#2.0-preview", "PolymerElements/*", etc]`
  Repositories to test.

* `"skip-test": ["PolymerElements/iron-meta", "*/*-alpha", etc]`
  Repositories not to test.  Filters out items from the `test` list.

* `"repo": ["PolymerElements/iron-list", "PolymerElements/paper-*", etc]`
  Explicit repos to clone into workspace, but not test.  This is useful if you
  want to force a specific version of a web package that wouldn't be installed
  by default.

* `"exclude-repo": ["PolymerElements/style-guide", "*/*-deprecated", etc]`
  Repositories not to load.  Filters out items from the `repo` list.

* `"fresh": true|false`
  Clears the workspace for each run, i.e. will clone all repos from remote
  instead of updating local copies.

* `"github-token": "0123456789ABCDEF1337"`
  Provide a github token via this setting instead of using "github-token" file.

* `"latest-release": true|false`
  Set to update repos to their latest release when possible and when a specific
  `#ref` is not included in their name.

* `"verbose": true|false`
  When true, output all the things.

* `"wct-flags": ["--local", "chrome"]`
  Set to specify flags passed to wct.

* `"workspace-dir": "/tmp/tattoo-workspace"`
  Specify a different target folder to clone repos and run web-components-tester
  from.

***Clean up installation***
```
rm -rf tattoo_workspace
```

***Or start with a fresh workspace as part of command***
```
tattoo -f
```

***Get help on the cli***
```
tattoo -h

tattoo (test all the things over & over)

  Runs the web-components-tester on custom element git repositories.

  Run test for a specific GitHub repository:
  $ tattoo PolymerElements/paper-button

  Run test for a whole bunch of GitHub repositories:
  $ tattoo PolymerElements/paper-*

  See more examples at https://github.com/Polymer/tattoo

Options

  -t, --test string                 Repositories to test. (This is the default
                                    option, so the --test/-t switch itself is
                                    not required.)
  -s, --skip-test string            Repositories not to test. Overrides the
                                    values from the --test
  -r, --repo string                 Explicit repos to load. Specifying explicit
                                    repos will disablerunning on the default set
                                    of repos for the user.
  -e, --exclude-repo string         Repositories not to load. Overrides the
                                    values from the --repo flag.
  -f, --fresh true|false            Set to clone all repos from remote instead
                                    of updating local copies.
  -c, --config-file string          Specify path to a json file which contains
                                    base configuration values. Command-line
                                    options flags supercede values in file where
                                    they differ. If file is missing, Tattoo will
                                    ignore.
  -g, --github-token string         Provide github token via command-line flag
                                    instead of "github-token" file.
  -l, --latest-release true|false   Set to update repos to the latest release
                                    when possible.
  -v, --verbose true|false          Set to print output from failed tests.
  -w, --wct-flags string            Set to specify flags passed to wct.
  -d, --workspace-dir string        Override the default path "repos" where the
                                    repositories will be cloned and web-
                                    components-tester will run.
  -h, --help true|false             Print this usage example.
```
