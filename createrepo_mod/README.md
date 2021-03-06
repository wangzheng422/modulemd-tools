# createrepo_mod

A small wrapper around `createrepo_c` and `modifyrepo_c` to provide an easy tool
for generating module repositories.

This tool can be used as a drop-in replacement for `createrepo_c` with
one caveat. You need to specify `<directory>` before `[options]`.
Otherwise it works fine with both module and non-module repositories.

Please see `man createrepo_c` for the complete list of possible
command-line arguments and their meaning. `createrepo_mod` doesn't
define or redefine any of the original `createrepo_c` arguments.


## Creating a modular repository

Please see the official Fedora Modularity documentation for the reference of how
module repositories should be created

https://docs.fedoraproject.org/en-US/modularity/hosting-modules/

Even though the process of creating modular repositories takes only
few simple steps, from user perspective the whole action is atomic.
That's where `createrepo_mod` fits in.


## Usage

First navigate to a directory that is to become a repository. You
should have some RPM packages in there. In order to create a modular
repository (instead of a normal one), you need to put a module YAML
file into the directory.

```
$ ls -1
foo-1.0.fc33.noarch.rpm
foo:devel:123:f32:x86_64.modulemd.yaml
```

There can be multiple RPM files in the directory (which is no
surprise) but there can also be multiple module YAML files. Gziped
YAML files, e.g. `foo:devel:123:f32:x86_64.modulemd.yaml.gz` are also
suppoprted. Each `.yaml` and `.yaml.gz` in the directory is examined
and used only if it a valid [modulemd document], otherwise it is
skipped. Please see
https://github.com/fedora-modularity/libmodulemd/blob/main/yaml_specs/modulemd_stream_v2.yaml

Having RPM packages and module YAML documents, simply run

```
$ createrepo_mod .
```


## Debug

If you are having troubles with module repositories not working, check
your generated `repodata` directory. There should be a compressed file
with module metadata.

```
$ ls repodata/*-modules.yaml.gz
repodata/34bd2ebb4de3e21644b351d06b59783dcd1aa751b035bbb31da70fa62dbb8e97-modules.yaml.gz
```

If it is missing you either didn't put a valid modulemd YAML document
into your repo directory or you have encountered a bug in
`createrepo_mod` tool.

Also, the initial module YAML files in the repo directory required for
repo generation (e.g. `foo:devel:123:f32:x86_64.modulemd.yaml.gz`) and
`modules.yaml` file generated by `createrepo_mod` run are not
necessary for the repo to work and can be safely removed. They might
be useful for a repo re-generation though. The only important thing is
the content of `repodata` directory.


## Future

This is supposed to be only a temporary solution, in the future we would like to
have the modularity support implemented in `createrepo_c` itself. See

https://bugzilla.redhat.com/show_bug.cgi?id=1816753

This feature is built-in in the `createrepo_c` itself since 0.16.1 version.
