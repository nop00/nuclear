# Nuclear :atom_symbol:

_[![nuget-clear version](https://img.shields.io/nuget/v/nuget-clear.svg?style=flat&label=NuGet:%20nuget-clear-fork)](https://www.nuget.org/packages/nuget-clear)_

Does your NuGet package have too many versions? Do you want to bulk delete or unlist them? Do it quickly using [Nuclear](https://www.nuget.org/packages/nuget-clear-fork)!

## Installation

You can install `nuclear` as a [.NET tool](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools):

```
dotnet tool install --global nuget-clear-fork --version 1.0.0-preview3
```

## Examples

Let's find some versions of your package to delete on nuget.org:

```
nuclear list My.Package
```

Too many results? Try filtering the package list using [floating version notation](#version-ranges):

```
nuclear list My.Package 1.1.*
```

Let's nuke some packages! 🤯

```
nuclear delete My.Package 2.0.0-* --api-key NUGET_API_KEY
```

This [unlists](https://docs.microsoft.com/nuget/nuget-org/policies/deleting-packages) pre-releases of `My.Package` v2.0.0 on nuget.org.

You can create API keys on nuget.org using [this documentation](https://docs.microsoft.com/nuget/nuget-org/publish-a-package#create-api-keys). Make sure to select the `Unlist package` scope when creating your API key.

## Concepts

### Deleting vs unlisting

NuGet servers are free to interpret "delete" operations. For example, [nuget.org unlists packages](https://docs.microsoft.com/nuget/nuget-org/policies/deleting-packages) to prevent the ["left-pad problem"](https://blog.npmjs.org/post/141577284765/kik-left-pad-and-npm). Unlisted packages are undiscoverable on nuget.org and can be re-listed in the future.

However, private NuGet servers like [BaGet can be configured to delete packages](https://loic-sharma.github.io/BaGet/configuration/#enable-package-hard-deletions). You may not be able to undo a package deletion, so be careful!

### Version ranges

NuGet packages use [semantic versioning](https://semver.org/) which has the form `Major.Minor.Patch[-PreleaseLabel]`. A package is considered *pre-release* if it has a pre-release label, or *stable* otherwise. For example, version `1.0.0-preview` is a pre-release, but version `1.0.0` is stable.

Nuclear supports floating notation. For example:

Version range | Matches | Ignores | Notes
-- | -- | -- | --
1.0.0 | 1.0.0 | 2.0.0 | Exact version match
1.0.\* | 1.0.0, 1.0.1 | 1.1.0, 2.0.0,<br />1.0.0-prerelease | Excludes pre-releases
1.\* | 1.0.0, 1.2.0 | 2.0.0,<br />1.0.0-prerelease | Excludes pre-releases
\* | 0.0.0, 1.2.3 | 1.0.0-prerelease | Matches all stable versions
1.0.0-\* | 1.0.0-alpha, 1.0.0-beta | 1.0.0 | Excludes stable releases
\*-\* | 1.0.0-alpha, 3.4.5-beta | 1.0.0 | Matches all pre-release versions

## Reference

### nuclear list

Find package versions. Usage:

```
nuclear list <PACKAGE> [VERSIONS] [OPTIONS]
```

Arguments:

* `PACKAGE` - The package ID whose versions should be listed.
* `VERSIONS` - Optional. Filters results using [floating version notation](#version-ranges).

Options:

* `-s, --source` - The NuGet package source URL. Defaults to `https://api.nuget.org/v3/index.json`.
* `-o, --output` - Set the output format. Allowed values are Allowed values are `tty`, or `json`.

### nuclear delete

Delete package versions. Usage:

```
nuclear delete <PACKAGE> <VERSIONS> [OPTIONS]
```

Arguments:

* `PACKAGE` - The package ID whose versions should be deleted.
* `VERSIONS` - The version range that should be deleted using [floating version notation](#version-ranges).

Options:

* `-s, --source` - The NuGet package source URL. Defaults to `https://api.nuget.org/v3/index.json`.
* `-k, --api-key` - The API key to authenticate on the package source.
* `--dry-run` - Show what would be deleted.

## About this fork
I wanted to remove all versions from certain packages, but the command kept stopping removing versions whenever an error occured (usually a 404 on a package version), so this fork just wraps the delete command in a `try..catch` in order to keep it going.
