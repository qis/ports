# Ports
Vcpkg ports registry.

## Instructions
Use a project like [qis/template](https://github.com/qis/template) to add or modify a port using a local ports overlay
until it is stable enough to be added to the registry, then increase `"port-version"` in the port `vcpkg.json` file.
Document changes in the `readme.md` file inside every port directory.

```sh
# Stage port changes.
git add ports/spdlog

# Update port manifest.
vcpkg format-manifest ports/spdlog/vcpkg.json

# Update database.
vcpkg x-add-version --x-builtin-ports-root=./ports --x-builtin-registry-versions-dir=./versions spdlog

# Stage database changes.
git add versions

# Commit changes with the following message:
# [<port>-<version(-semver|-date|-string|)>(#port-version|) changes description
git commit -m "[spdlog-1.15.3#1] removed fmt dependency"
```
