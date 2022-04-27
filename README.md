ior
====

![ior](http://i.imgur.com/yxN7Hty.jpg)

### usage

```
go get github.com/vektah/ior

cd $GOPATH/src/github.com/you/yourapp
ior -listen ":1234" -- [args passed through to your app]
```

### What it does

1. Wait for a request.
2. Hashes all go files, excluding vendor, and other common large directories
3. If the hash changes, do a `go install`, show errors if it fails otherwise restart the server

Thats it. No inotify, no fsevents, no polling, no CPU chewing. 100% docker-machine friendly. 100% nfs friendly.

### How long does it take really?

On a project with ~30k lines across ~300 files it adds about 50ms to every request, this is just the time it takes to hash.

Because its using `go install` rather than `go build` it also leverages the pkg cache, so small changes are pretty quick to compile.

### Related projects
 - https://github.com/codegangsta/gin: This tool works in docker but uses a poll loop checking modified timestamps. This causes constant cpu load while running and nfs can cache the metadata.
 - https://github.com/pilu/fresh: Super configurable with nice output, but only uses fsevents/inotify so wont work over nfs boundaries.
 - https://github.com/tockins/realize: Full build system, lots of knobs, runs your tests, uses fsevents/inotify.

## Releases

This uses (GoReleaser](https://goreleaser.com) to build and release go binaries. There is a GitHub Actions workflow configured to run this on any new tags pushed. In order to create a new release, from the master branch:

```
git tag vx.x.x e.g. v1.0.0
git push --tags
```

This should trigger the workflow to automatically create a new release. Once it has run, you should see a new release at https://github.com/99designs/ior/releases. Currently GoReleaser is configured to autogenerate a changelog based on commits.

The configuration for GoReleaser is in [.goreleaser.yaml](./.goreleaser.yaml). This specifies what operating systems and architectures to build for along with any other customization we want. You can read more about what can be configured here: https://goreleaser.com/customization/
