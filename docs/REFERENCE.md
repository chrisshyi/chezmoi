# chezmoi Reference Manual

Manage your dotfiles securely across multiple machines.

* [Concepts](#concepts)
* [Global command line flags](#global-command-line-flags)
  * [`--color` *value*](#--color-value)
  * [`-c`, `--config` *filename*](#-c---config-filename)
  * [`-D`, `--destination` *directory*](#-d---destination-directory)
  * [`-n`, `--dry-run`](#-n---dry-run)
  * [`-h`, `--help`](#-h---help)
  * [`-S`, `--source` *directory*](#-s---source-directory)
  * [`-v`, `--verbose`](#-v---verbose)
  * [`--version`](#--version)
* [Configuration file](#configuration-file)
  * [Configuration variables](#configuration-variables)
* [Attributes](#attributes)
* [Commands](#commands)
  * [`add` *targets*](#add-targets)
    * [`-e`, `--empty`](#-e---empty)
    * [`-x`, `--exact`](#-x---exact)
    * [`-p`, `--prompt`](#-p---prompt)
    * [`-r`, `--recursive`](#-r---recursive)
    * [`-T`, `--template`](#-t---template)
    * [`add` examples](#add-examples)
  * [`apply` [*targets*]](#apply-targets)
    * [`apply` examples](#apply-examples)
  * [`archive`](#archive)
    * [`archive` examples](#archive-examples)
  * [`cat` [targets]](#cat-targets)
    * [`cat` examples](#cat-examples)
  * [`cd`](#cd)
    * [`cd` examples](#cd-examples)
  * [`chattr` *attributes* *targets*](#chattr-attributes-targets)
    * [`chattr` examples](#chattr-examples)
  * [`completion` *shell*](#completion-shell)
    * [`completion` examples](#completion-examples)
  * [`data`](#data)
    * [`-f`, `--format` *format*](#-f---format-format)
    * [`data` examples](#data-examples)
  * [`diff` *targets*](#diff-targets)
    * [`diff` examples](#diff-examples)
  * [`doctor`](#doctor)
    * [`doctor` examples](#doctor-examples)
  * [`dump` *targets*](#dump-targets)
    * [`-f` / `--format` *format*](#-f----format-format)
    * [`dump` examples](#dump-examples)
  * [`edit` *targets*](#edit-targets)
    * [`-a`, `--apply`](#-a---apply)
    * [`-d`, `--diff`](#-d---diff)
    * [`-p` / `--prompt`](#-p----prompt)
    * [`edit` examples](#edit-examples)
  * [`edit-config`](#edit-config)
    * [`edit-config` examples](#edit-config-examples)
  * [`forget` *targets*](#forget-targets)
    * [`forget` examples](#forget-examples)
  * [`help` *command*](#help-command)
  * [`init` [*repo*]](#init-repo)
    * [`init` examples](#init-examples)
  * [`import` *filename*](#import-filename)
    * [`--destination` *directory*](#--destination-directory)
    * [`-x` / `--exact`](#-x----exact)
    * [`-r`, `--remove-destination`](#-r---remove-destination)
    * [`--strip-components` *n*](#--strip-components-n)
    * [`import` examples](#import-examples)
  * [`merge` *targets*](#merge-targets)
    * [`merge` examples](#merge-examples)
  * [`remove`, `rm` *targets*](#remove-rm-targets)
  * [`secret`](#secret)
  * [`source` [*args*]](#source-args)
    * [`source` examples](#source-examples)
  * [`source-path` [*targets*]](#source-path-targets)
    * [`source-path` examples](#source-path-examples)
  * [`unmanaged`](#unmanaged)
    * [`unmanaged` examples](#unmanaged-examples)
  * [`update`](#update)
    * [`update` examples](#update-examples)
  * [`upgrade`](#upgrade)
    * [`upgrade` examples](#upgrade-examples)
  * [`verify` [*targets*]](#verify-targets)
    * [`verify` examples](#verify-examples)
* [Editor configuration](#editor-configuration)
* [Umask configuration](#umask-configuration)
* [Templates](#templates)
  * [Variables](#variables)
  * [Functions](#functions)

## Concepts

chezmoi evaluates the source state for the current machine and then updates the
destination directory, where:

* The *source state* declares the desired state of your home directory,
  including templates and machine-specific configuration.

* The *source directory* is where chezmoi stores the source state, by default
  `~/.local/share/chezmoi`.

* The *target state* is the source state computed for the current machine.

* The *destination directory* is the directory that chezmoi manages, by default
  `~`, your home directory.

* A *target* is a file, directory, or symlink in the destination directory.

* The *destination state* is the state of all the targets in the destination
  directory.

* The *config file* contains machine-specific configuration, by default it is
  `~/.config/chezmoi/chezmoi.toml`.

## Global command line flags

Command line flags override any values set in the configuration file.

### `--color` *value*

Colorize diffs, *value* can be `on`, `off`, or `auto`. The default value is
`auto` which will colorize diffs only if the output is a terminal.

### `-c`, `--config` *filename*

Read the configuration from *filename*.

### `-D`, `--destination` *directory*

Use *directory* as the destination directory.

### `-n`, `--dry-run`

Set dry run mode. In dry run mode, the destination directory is never modified.
This is most useful in combination with the `-v` (verbose) flag to print changes
that would be made without making them.

### `-h`, `--help`

Print help.

### `-S`, `--source` *directory*

Use *directory* as the source directory.

### `-v`, `--verbose`

Set verbose mode. In verbose mode, chezmoi prints the changes that it is making
as approximate shell commands, and any differences in files between the target
state and the destination set are printed as unified diffs.

### `--version`

Print the version of chezmoi, the commit at which it was built, and the build
timestamp.

## Configuration file

chezmoi searches for its configuration file according to the [XDG Base Directory
Specification](https://standards.freedesktop.org/basedir-spec/basedir-spec-latest.html)
and supports all formats supported by
[`github.com/spf13/viper`](https://github.com/spf13/viper), namely JSON, TOML,
YAML, macOS property file format, and HCL. The basename of the config file is
chezmoi, and the first config file found is used.

### Configuration variables

The following configuration variables are available:

| Variable           | Type     | Default value             | Description                       |
| ------------------ | -------- | ------------------------- | --------------------------------- |
| `color`            | string   | `auto`                    | Colorize diffs                    |
| `data`             | any      | none                      | Template data                     |
| `dryRun`           | boolean  | `false`                   | Dry run mode                      |
| `gpgRecipient`     | string   | none                      | GPG recipient                     |
| `merge.command`    | string   | `vimdiff`                 | 3-way merge command               |
| `merge.args`       | []string | none                      | Extra args to 3-way merge command |
| `sourceDir`        | string   | `~/.config/share/chezmoi` | Source directory                  |
| `sourceVCSCommand` | string   | `git`                     | Source version control system     |
| `targetDir`        | string   | `~`                       | Target directory                  |
| `umask`            | integer  | from system               | Umask                             |
| `verbose`          | boolean  | `false`                   | Verbose mode                      |

In addition, a number of secret manager integrations add configuration
variables. These are documented in the secret manager section.

## Attributes

chezmoi stores the source state of files, symbolic links, and directories in
regular files and directories in the source directory (`~/.local/share/chezmoi`
by default). This location can be overridden with the `-S` flag or by giving a
value for `sourceDir` in `~/.config/chezmoi/chezmoi.toml`.  Some state is
encoded in the source names. chezmoi ignores all files and directories in the
source directory that begin with a `.`. The following prefixes and suffixes are
special, and are collectively referred to as "attributes":

| Prefix/suffix        | Effect                                                                            |
| -------------------- | ----------------------------------------------------------------------------------|
| `encrypted_` prefix  | Encrypt the file in the source state.                                             |
| `private_` prefix    | Remove all group and world permissions from the target file or directory.         |
| `empty_` prefix      | Ensure the file exists, even if is empty. By default, empty files are removed.    |
| `exact_` prefix      | Remove anything not managed by chezmoi.                                           |
| `executable_` prefix | Add executable permissions to the target file.                                    |
| `symlink_` prefix    | Create a symlink instead of a regular file.                                       |
| `dot_` prefix        | Rename to use a leading dot, e.g. `dot_foo` becomes `.foo`.                       |
| `.tmpl` suffix       | Treat the contents of the source file as a template.                              |

Order is important, the order is `exact_`, `private_`, `empty_`, `executable_`,
`symlink_`, `dot_`, `.tmpl`.

Different target types allow different prefixes and suffixes:

| Target type   | Allowed prefixes and suffixes                                      |
| ------------- | ------------------------------------------------------------------ |
| Directory     | `exact_`, `private_`, `dot_`                                       |
| Regular file  | `encrypted_`, `private_`, `empty_`, `executable_`, `dot_`, `.tmpl` |
| Symbolic link | `symlink_`, `dot_`, `.tmpl`                                        |

## Commands

### `add` *targets*

Add *targets* to the source state. If any target is already in the source state,
then its source state is replaced with its current state in the destination
directory. The `add` command accepts additional flags:

#### `-e`, `--empty`

Set the `empty` attribute on added files.

#### `-x`, `--exact`

Set the `exact` attribute on added directories.

#### `-p`, `--prompt`

Interactively prompt before adding each file.

#### `-r`, `--recursive`

Recursively add all files, directories, and symlinks.

#### `-T`, `--template`

Set the `template` attribute on added files and symlinks. In addition, chezmoi
attempts to automatically generate the template by replacing any template data
values with the equivalent template data keys. Longer subsitutions occur before
shorter ones.

#### `add` examples

    chezmoi add ~/.bashrc
    chezmoi add ~/.gitconfig --template
    chezmoi add ~/.vim --recursive
    chezmoi add ~/.oh-my-zsh --exact --recursive

### `apply` [*targets*]

Ensure that *targets* are in the target state, updating them if necessary. If no
targets are specified, the state of all targets are ensured.

#### `apply` examples

    chezmoi apply
    chezmoi apply --dry-run --verbose
    chezmoi apply ~/.bashrc

### `archive`

Write a tar archive of the target state to stdout. This can be piped into `tar`
to inspect the target state.

#### `archive` examples

    chezmoi archive | tar tvf -

### `cat` [targets]

Write the target state of *targets*  to stdout. *targets* must be files or
symlinks. For files, the target file contents are written. For symlinks, the
target target is written.

#### `cat` examples

    chezmoi cat ~/.bashrc

### `cd`

Launch a shell in the source directory.

#### `cd` examples

    chezmoi cd

### `chattr` *attributes* *targets*

Change the attributes of *targets*. *attributes* specifies which attributes to
modify. Add attributes by specifying them or their abbreviations directly,
optionally prefixed with a plus sign (`+`). Remove attributes by prefixing them
or their attributes with the string `no` or a minus sign (`-`). The available
attributes and their abbreviations are:

| Attribute    | Abbreviation |
| ------------ | ------------ |
| `empty`      | `e`          |
| `encrypted`  | none         |
| `exact`      | none         |
| `executable` | `x`          |
| `private`    | `p`          |
| `template`   | `t`          |

Multiple attributes modifications may be specified by separating them with a
comma (`,`).

#### `chattr` examples

    chezmoi chattr template ~/.bashrc
    chezmoi chattr noempty ~/.profile
    chezmoi chattr private,template ~/.netrc

### `completion` *shell*

Output shell completion code for the specified shell (`bash` or `zsh`).

#### `completion` examples

    chezmoi completion bash
    chezmoi completion zsh

### `data`

Write the computed template data in JSON format to stdout. The `data` command
accepts additional flags:

#### `-f`, `--format` *format*

Print the computed template data in the given format. The accepted formats are
`json` (JSON), `toml` (TOML), and `yaml` (YAML).

#### `data` examples

    chezmoi data
    chezmoi data --format=yaml

### `diff` *targets*

Print the approximate shell commands required to ensure that *targets* in the
destination directory match the target state. If no targets are specifed, print
the commands required for all targets. It is equivalent to `chezmoi apply
--dry-run --verbose`.

#### `diff` examples

    chezmoi diff
    chezmoi diff ~/.bashrc

### `doctor`

Check for potential problems.

#### `doctor` examples

    chezmoi doctor

### `dump` *targets*

Dump the target state in JSON format. If no targets are specified, then the
entire target state. The `dump` command accepts additional arguments:

#### `-f` / `--format` *format*

Print the target state in the given format. The accepted formats are `json`
(JSON) and `yaml` (YAML).

#### `dump` examples

    chezmoi dump ~/.bashrc
    chezmoi dump --format=yaml

### `edit` *targets*

Edit the source state of *targets*, which must be files or symlinks. The `edit`
command accepts additional arguments:

#### `-a`, `--apply`

Apply target immediately after editing.

#### `-d`, `--diff`

Print the difference between the target state and the actual state after
editing.

#### `-p` / `--prompt`

Prompt before applying each target.

#### `edit` examples

    chezmoi edit ~/.bashrc
    chezmoi edit ~/.bashrc --apply --prompt

### `edit-config`

Edit the configuration file.

#### `edit-config` examples

    chezmoi edit-config

### `forget` *targets*

Remove *targets* from the source state, i.e. stop managing them.

#### `forget` examples

    chezmoi forget ~/.bashrc

### `help` *command*

Print the help associated with *command*.

### `init` [*repo*]

Setup the source directory and update the destination directory to match the
target state. If *repo* is given then it is checked out into the source
directory, otherwise a new repository is initialized in the source directory. If
a file called `.chezmoi.format.tmpl` exists, where `format` is one of the
supported file formats (e.g. `json`, `toml`, or `yaml`) then a new configuration
file is created using that file as a template. Finally, if the `--apply` flag is
passed, `chezmoi apply` is run.

#### `init` examples

    chezmoi init https://github.com/user/dotfiles.git
    chezmoi init https://github.com/user/dotfiles.git --apply

### `import` *filename*

Import the source state from an archive file in to a directory in the source
state. This is primarily used to make subdirectories of your home directory
exactly match the contents of a downloaded archive. You will generally always
want to set the `--destination`, `--exact`, and `--remove-destination` flags.

The only supported archive format is `.tar.gz`.

#### `--destination` *directory*

Set the destination (in the source state) where the archive will be imported.

#### `-x` / `--exact`

Set the `exact` attribute on all imported directories.

#### `-r`, `--remove-destination`

Remove destination (in the source state) before importing.

#### `--strip-components` *n*

Strip *n* leading components from paths.

#### `import` examples

    curl -s -L -o oh-my-zsh-master.tar.gz https://github.com/robbyrussell/oh-my-zsh/archive/master.tar.gz
    chezmoi import --strip-components 1 --destination ~/.oh-my-zsh oh-my-zsh-master.tar.gz

### `merge` *targets*

Perform a three-way merge between the destination state, the source state, and
the target state. The merge tool is defined by the `merge.command` configuration
variable, and defaults to `vimdiff`. If multiple targets are specified the merge
tool is invoked for each target. If the target state cannot be computed (for
example if source is a template containing errors or an encrypted file that
cannot be decrypted) a two-way merge is performed instead.

#### `merge` examples

    chezmoi merge ~/.bashrc

### `remove`, `rm` *targets*

Remove *targets* from both the source state and the destination directory.

### `secret`

Interact with a secret manager. See the "Secret managers" section for details.

### `source` [*args*]

Execute the source version control system in the source directory with *args*.
Note that any flags for the source version control system must be sepeated with
a `--` to stop chezmoi from reading them.

#### `source` examples

    chezmoi source init
    chezmoi source add .
    chezmoi source commit -- -m "Initial commit"

### `source-path` [*targets*]

Print the path to each target's source state. If no targets are specified then
print the source directory.

#### `source-path` examples

    chezmoi source-path
    chezmoi source-path ~/.bashrc

### `unmanaged`

List all unmanaged files in the destination directory.

#### `unmanaged` examples

    chezmoi unmanaged

### `update`

Pull changes from the source VCS and apply any changes.

#### `update` examples

    chezmoi update

### `upgrade`

Upgrade chezmoi by downloading and installing a new version. This will call the
Github API to determine if there is a new version of chezmoi available, and if
so, download and attempt to install it in the same way as chezmoi was previously
installed.

If chezmoi was installed with a package manager (`dpkg` or `rpm`) then `upgrade`
will download a new package and install it, using `sudo` if it is installed.
Otherwise, chezmoi will download the latest executable and replace the existing
exectuable with the new version.

If the `CHEZMOI_GITHUB_API_TOKEN` environment variable is set, then its value
will be used to authenticate requests to the Github API, otherwise
unauthenticated requests are used which are subject to stricter [rate
limiting](https://developer.github.com/v3/#rate-limiting). Unauthenticated
requests should be sufficient for most cases.

#### `upgrade` examples

    chezmoi upgrade

### `verify` [*targets*]

Verify that all *targets* match their target state. chezmoi exits with code 0
(success) if all targets match their target state, or 1 (failure) otherwise. If
no targets are specified then all targets are checked.

#### `verify` examples

    chezmoi verify
    chezmoi verify ~/.bashrc

## Editor configuration

The `edit` and `edit-config` commands use the editor specified by the `VISUAL`
environment variable, the `EDITOR` environment variable, or `vi`, whichever is
specified first.

## Umask configuration

By default, chezmoi uses your current umask as set by your operating system and
shell. chezmoi only stores crude permissions in its source state, namely in the
`executable`  and `private` attributes, corresponding to the umasks of `0111`
and `0077` respectively.

For machine-specifc control of umask, set the `umask` configuration variable in
chezmoi's configuration file.

## Templates

### Variables

chezmoi provides the following automatically populated variables:

| Variable                | Value                                                                                                                  |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `.chezmoi.arch`         | Architecture, e.g. `amd64`, `arm`, etc. as returned by [runtime.GOARCH](https://godoc.org/runtime#pkg-constants).      |
| `.chezmoi.fullHostname` | The full hostname of the machine chezmoi is running on.                                                                |
| `.chezmoi.group`        | The group of the user running chezmoi.                                                                                 |
| `.chezmoi.homedir`      | The home directory of the user running chezmoi.                                                                        |
| `.chezmoi.hostname`     | The hostname of the machine chezmoi is running on, up to the first `.`.                                                |
| `.chezmoi.os`           | Operating system, e.g. `darwin`, `linux`, etc. as returned by [runtime.GOOS](https://godoc.org/runtime#pkg-constants). |
| `.chezmoi.osRelease`    | The information from `/etc/os-release`, Linux only, run `chezmoi data` to see its output.                              |
| `.chezmoi.username`     | The username of the user running chezmoi.                                                                              |

### Functions

All standard [`text/template`](https://godoc.org/text/template) and [hermetic
text template functions from `sprig`](http://masterminds.github.io/sprig/) are
included.

When generating the initial config file, the function `promptString` is also
available. The single argument is a string prompted to the user, and the return
value is the user's response to that prompt.

`.chezmoi.yaml.tmpl` example:

    {{ $email := promptString "email" -}}
    data:
        email: "{{ $email }}"
