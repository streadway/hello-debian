# hello-debian

A tutorial for repository management of debian packages.  This project installs
`hello-world`, compiled from 'hello.c' into `/usr/bin`.

Maintaining upstream packages with git has
[some](https://github.com/freshtonic/debianization-with-git-howto) good
overviews.

# Setup Jenkins

  * New Project: (example) hello-debian
  * Source Code Management / Git: check
  * Repository URL: (example) `https://github.com/streadway/hello-debian.git`
  * Branches to build: `*/master`
  * Build / Execute Shell: `env`

Confirm Jenkins can build the project with *Build Now*.

# Maintain the original/upstream

Write your software.

In this example, the original is `hello.c` built with `Makefile`, producing `hello-world`.

```
make hello-world
```

## Update Jenkins to build the original

  * Build / Execute Shell: make

Push your original changes in the master branch.

Confirm Jenkins can build the project with *Build Now*.

# Setup debian/

Debian builds start by producing a source package `*.dsc`, then compiling that
to a binary package `*.deb`.  This example is built as a maintainer package,
with the pristine origin and debian control files in different branches.

Files in debian/ follow the package maintainer policy.  The [bare minimum
files](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html) consists
of:

  * [`compat`](#compat) contains version of the `debhelper` build tools required.
  * [`copyright`](#copyright) contains legal guidance for distribution.
  * [`rules`](#rules) is the executable script called by `debhelper` tools to build the original.
  * [`control`](#control) contains rfc822 encoded description of the package and its dependencies.
  * [`changelog`](#changelog) contains the history of the package and its versions.
  * [`install`](#install) contains the artifacts of the build and where to put them.

## (Optional) `dh_make`

From the `apt-get install dh-make` package, you could use `dh_make` to setup `debian/` with many example files.  To use `dh_make` do the following:

```
git archive master | gzip > ../hello-debian_0.0.1.tar.gz
dh_make -p hello-debian_0.0.1 -f ../hello-debian_0.0.1.tar.gz
```

Instead, this example prepares the `debian/` directory by hand.

## compat

This file tells the `debhelper` tools how to behave when executing `rules`.
For compatability with squeeze and wheezy toolchains, use `8`, and add a build
dependency to `debhelper (>= 8)` to the [`control`](#control) file.

```
8
```


## copyright

This is the copyright and license of the upstream.  Unless you're going to
submit this package to the debian pool, use the copyright of the original.  If
that doesn't exist then use a non-free license:

```
Copyright (c) {first year of package} {copyright owner} All Rights Reserved.
```

For public projects or projects that have different copyrights for different
file sets, use a richer format:

```
Format: http://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: hello-debian
Source: http://github.com/streadway/hello-debian

Files: *
Copyright: 2014 SoundCloud Ltd.
License: GPL-2+
 This package is free software; you can redistribute it and/or modify
 it under the terms of the GNU General Public License as published by
 the Free Software Foundation; either version 2 of the License, or
 (at your option) any later version.
 .
 This package is distributed in the hope that it will be useful,
 but WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 GNU General Public License for more details.
 .
 You should have received a copy of the GNU General Public License
 along with this program. If not, see <http://www.gnu.org/licenses/>
 .
 On Debian systems, the complete text of the GNU General
 Public License version 2 can be found in "/usr/share/common-licenses/GPL-2".
```

## rules

This file is where most of the work is done to build package using the debian build toolchain.

The minimal `rules` delegates each command to the `debhelper` tool `dh` is:

```
#!/usr/bin/make -f
%:
	dh $@
```

`debhelper` will detect the `Makefile` and run `make` for the build `dh_auto_build` step.

## control

This file contains the metadata for all packages that are built from this
repository.  At a minimum, it contains the following to build a source and
binary package:

A full list of the fields can be found with `man deb-control`.  This file
starts with a source description and then package descriptions that reference
the source.

```
Source: hello-debian
Section: utils
Priority: extra
Maintainer: Full Name <yourname@example.com>
Build-Depends: debhelper (>= 8.0.0)
Standards-Version: 3.9.3
Vcs-Git: git@github.com:streadway/hello-debian.git
Vcs-Browser: http://github.com/streadway/hello-debian

Package: hello-debian
Section: utils
Priority: extra
Architecture: any
Depends: ${shlibs:Depends}, ${misc:Depends}
Description: Example package maintenance (under 60 chars)
 The build output from a repository listing the steps to setup a debian
 package in a long-format under 80 characters per line.
```

## changelog

The `changelog` contains the history of the package usable by the package management tools.

Use `dch`/`debchange` to add package related messages to this.

Use `git-dch` to manage `debian/changelog` from git tags.

Start the repository with an UNRELEASED change in this example, later steps will use git tags.

```
hello-debian (0.0.1-1) UNRELASED; urgency=low

  * Initial build

 -- Full Name <yourname@example.com>  Sun, 23 Mar 2014 12:37:24 +0100
```

## install

The artifact of this package is `hello-debian` an executable that should go
into `/usr/bin`.  Use this file if you want to only install specific files.

```
hello-debian usr/bin
```

## Update Jenkins for the initial build

At this point, it's possible to build `hello-debian` using `dpkg-buildpackage -uc -us`.

  * Build / Execute Shell: `dpkg-buildpackage -uc -us`

Push your original changes in the master branch.

Confirm Jenkins can build the project with *Build Now*.
