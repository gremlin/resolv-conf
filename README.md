Gremlin
=======

This section contains notes for using this fork when building Agent.

The expectation is that this fork will be kept synchronized with the upstream repository.  The
upstream is quite stable so updates are expected to be infrequent.

Automating the synchronization is not worth the effort.  It will take a few hours to get that wrung
out and about 30 minutes to document the process.

## Prepare

The local computer has to be prepared.  This only needs to be done once.  The steps below...

``` bash
# Clone the fork
git clone git@github.com:gremlin/resolv-conf.git
# Navigate to the clone
cd resolv-conf
# Add a remote
git remote add upstream https://github.com/tailhook/resolv-conf.git
# Check the remotes
git remote -v
```

That's it.  The local copy is ready for synchronizing.

## Synchronize Master

Navigate to the clone...
`cd resolv-conf`

Ensure the `master` branch is active...
`git checkout master`

Fetch any changes...
`git fetch upstream`

Merge those changes...
`git merge upstream/master`

Push changes back to our fork...
`git push`

## Synchronize Work-in-progress

For each branch that has work-in-progress do the following.  At the time this was written there is
just one branch: `pag/add-no-strict`

Switch to the work-in-progress branch...
`git checkout pag/add-no-strict`

Ensure the local copy is up-to-date...
`git pull`

Merge changes from the upstream repository...
`git merge master`

Push changes back to our fork...
`git push`

## Freshen our build-from-this-branch with the appropriate work-in-progress

Agent is built from the `gremlin` branch.  This last step merges work-in-progress into the `gremlin`
branch.

Switch to the `gremlin` branch...
`git checkout gremlin`

Ensure the local copy is up-to-date...
`git pull`

Merge each work-in-progress branch...
`git merge pag/add-no-strict`

Push changes back to our fork...
`git push`

## Using

Using the changes is fairly easy.  In the root `Cargo.toml` file add the following to enable
`no-strict`.

``` TOML
[patch.crates-io]
resolv-conf = {version="0.7", git="https://github.com/gremlin/resolv-conf.git", branch="gremlin", features=["no-strict"]}
```

Build the project.  Carefully check the `Cargo.lock` file.  If everything worked then things that
depend on `resolv-conf` will have entries like the following where `resolv-conf` is simply included
in the `dependencies` list...

``` TOML
[[package]]
name = "trust-dns-resolver"
version = "0.19.6"
source = "registry+https://github.com/rust-lang/crates.io-index"
checksum = "6759e8efc40465547b0dfce9500d733c65f969a4cbbfbe3ccf68daaa46ef179e"
dependencies = [
 "backtrace",
 "cfg-if 0.1.10",
 "futures",
 "ipconfig",
 "lazy_static",
 "log 0.4.14",
 "lru-cache",
 "resolv-conf",
 "smallvec",
 "thiserror",
 "tokio",
 "trust-dns-proto",
]
```

This will be the entry for `resolv-conf`.  Note `source`.

``` TOML
[[package]]
name = "resolv-conf"
version = "0.7.0"
source = "git+https://github.com/gremlin/resolv-conf.git?branch=gremlin#58673b43ffebb5aab3beec67eae0dc53c5c66c2d"
dependencies = [
 "hostname",
 "quick-error 2.0.1",
]
```

Resolv-conf
===========

**Status: Beta**

A `/etc/resolv.conf` parser crate for rust.

Why?
====

1. There is no bare file parser in the crates.io at the moment
2. I needed one to make dns resolver for [rotor]

[rotor]: http://github.com/tailhook/rotor


License
=======

Licensed under either of

* Apache License, Version 2.0, (./LICENSE-APACHE or http://www.apache.org/licenses/LICENSE-2.0)
* MIT license (./LICENSE-MIT or http://opensource.org/licenses/MIT)

at your option.

Contribution
------------

Unless you explicitly state otherwise, any contribution intentionally
submitted for inclusion in the work by you, as defined in the Apache-2.0
license, shall be dual licensed as above, without any additional terms or
conditions.

