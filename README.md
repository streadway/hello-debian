# hello-debian

A tutorial for repository management of debian packages.  This project installs
`hello-world`, compiled from 'hello.c' into `/usr/bin`.

# Setup Jenkins

  * New Project: (example) hello-debian
  * Source Code Management / Git: check
  * Repository URL: (example) `https://github.com/streadway/hello-debian.git`
  * Branches to build: `*/master`
  * Build / Execute Shell: `env`

Confirm Jenkins can build the project with *Build Now*.

# Maintain the original/upstream

Write your software, or import an existing package into your master branch with
[`git-import-orig`](https://wiki.debian.org/PackagingWithGit#Using_a_tarball_file).

In this example, the original is `hello.c` built with `Makefile`, producing
`hello-world`.

```
make hello-world
```

## Update Jenkins to build the original

  * Build / Execute Shell: make

Push your original changes in the master branch.

Confirm Jenkins can build the project with *Build Now*.
