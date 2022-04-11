---
title: "Using Spack package manager for supercomputers to install software locally"
date: 2022-04-11T10:03:17+02:00
images: ["/blog/img/spack-logo-white.svg"]
tags: ["spack", "hpc", "linux"]
draft: false
---

I often find myself building software in different machines, with different configurations, architectures and compilers. Sometimes I just need to install some package in a server with not enough permissions to do so via the regular package manager.

Spack to the rescue!

Let's see an example where one wants to use [tmux]({{< relref "blog/tmux_workflow_nov21.md" >}}) in a machine where it is not installed, nor one has permissions to do so. Given that python is present in the system, and a compiler, one can try to compile tmux, to quickly find it will need several dependencies like libevent. Spack allows us to quickly check those dependencies:

```sh
spack info tmux
``` 

The `info` command will give us an overview of the *recipe*: a description, available versions and build and link dependencies. A *recipe* contains the instructions on how to build a particular package, and on installation Spack will proceed to compile the package and all its dependencies.

We can check the dependencies that will be built by checking the `spec` of the package:

```sh
spack spec tmux
```

This will show not only the dependency tree, but the concrete versions, compilers, architectures and variants used to get the package compiled:

```sh
Input spec
--------------------------------
tmux

Concretized
--------------------------------
tmux@3.2a%gcc@8.3.0 arch=linux-centos7-haswell
    ^libevent@2.1.12%gcc@8.3.0+openssl arch=linux-centos7-haswell
        ^openssl@1.1.1m%gcc@8.3.0~docs certs=system arch=linux-centos7-haswell
            ^perl@5.34.0%gcc@8.3.0+cpanm+shared+threads arch=linux-centos7-haswell
                ^berkeley-db@18.1.40%gcc@8.3.0+cxx~docs+stl patches=b231fcc arch=linux-centos7-haswell
                ^bzip2@1.0.8%gcc@8.3.0~debug~pic+shared arch=linux-centos7-haswell
                    ^diffutils@3.8%gcc@8.3.0 arch=linux-centos7-haswell
                        ^libiconv@1.16%gcc@8.3.0 libs=shared,static arch=linux-centos7-haswell
                ^gdbm@1.19%gcc@8.3.0 arch=linux-centos7-haswell
                    ^readline@8.1%gcc@8.3.0 arch=linux-centos7-haswell
                        ^ncurses@6.2%gcc@8.3.0~symlinks+termlib abi=none arch=linux-centos7-haswell
                            ^pkgconf@1.8.0%gcc@8.3.0 arch=linux-centos7-haswell
                ^zlib@1.2.11%gcc@8.3.0+optimize+pic+shared arch=linux-centos7-haswell
```

Here is where Spack shines: we can configure how the package and its dependencies will be built. A package and its dependencies will be built with the included compiler (a newer compiler can be compiled if needed) and installed locally, in a user accessible directory: no need for root permissions to install the software.

To customize how a package will be installed we can use several specifiers: `@` for versions, `%` for compilers, `^` for dependencies, `+` for variants and `~` or `-` to turn off variants.

Following the previous example, let say one wants to install tmux but the underlying libevent to not be built with openssl. The default variant is to use openssl as seen in the `spec` command: `^libevent@2.1.12%gcc@8.3.0+openssl`. To do se we could do the following:

```sh
spack spec tmux ^libevent -openssl
```

which will give us the following dependencies tree:

```sh
Input spec
--------------------------------
tmux
    ^libevent~openssl

Concretized
--------------------------------
tmux@3.2a%gcc@8.3.0 arch=linux-centos7-haswell
    ^libevent@2.1.12%gcc@8.3.0~openssl arch=linux-centos7-haswell
    ^ncurses@6.2%gcc@8.3.0~symlinks+termlib abi=none arch=linux-centos7-haswell
        ^pkgconf@1.8.0%gcc@8.3.0 arch=linux-centos7-haswell
```

Here we are saying: tell me the configuration and dependencies of installing tmux with its libevent dependency (`^`) but don't use the openssl variant for libevent (using `-` after the `^libevent`). The same could be done for any variant, compiler version, architecture and more.

If satisfied with the configuration we can go ahead and install the package:

```
spack install tmux ^libevent -openssl
spack load tmux
``` 

To check where the package is located we can use the `location` command or `load` the package and the check with something like `which`:

```sh
# show path where a package is installed
spack location -i tmux

# load package into environment variables
spack load tmux
# check its location
which tmux
```

We can even go ahead and install both variants:

```sh
spack install tmux
spack install tmux ^libevent -openssl
```

And then `load` to our environment variables the one to be used:

```sh
spack load tmux ^libevent -opensll
```

Both version will be installed in the system at the same time, and both can be used for different configurations. The same is true for all the possible configurations in case we need a package compiled by a specific compiler, or compiler version, or architecture, etc. With all packages living in the same system and loaded dynamically.