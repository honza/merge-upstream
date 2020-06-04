# merge_upstream

This is a simple utility to merge a branch from an upstream repository to
another branch in a downstream reposity.  This targets Golang repositories
specifically.

## usage

Export the following variables:

```sh
export UPSTREAM_REPO=https://github.com/metal3-io/baremetal-operator
export DOWNSTREAM_REPO=https://github.com/openshift/baremetal-operator
```

and optionally, override the branch names (defaults to `master`):

```sh
export UPSTREAM_BRANCH=xyz
export DOWNSTREAM_BRANCH=master
```

and run:

```sh
./merge_upstream
```

## how it works

We clone the upstream repo, and add the downstream repo as a git remote.  We
attempt the merge.  If it fails, we check if the conflicts are only in the go
module files (`go.mod`, `go.sum`, and the `vendor/` directory).

When merge conflicts are only present in the go module files, we prefer the
upstream version, and then we run `go mod tidy`, stage the changes, and continue
with the merge.  If the conflicts are in other files, we abort, and ask a human
to resolve the changes.

If everything succeeds, we place the changes into a branch, and attemt to push
it.
