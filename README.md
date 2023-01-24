# @kogs/nwjs
![tests status](https://github.com/Kruithne/kogs-nwjs/actions/workflows/github-actions-test.yml/badge.svg) [![license badge](https://img.shields.io/github/license/Kruithne/kogs-nwjs?color=blue)](LICENSE)

`@kogs/nwjs` is a command-line utility written in [Node.js](https://nodejs.org/) that streamlines the process of preparing a [nw.js](https://nwjs.io/) distribution.

This utility is intended for use in automated build pipelines or for development and testing purposes.

- Supports all platforms, architectures and versions.
- Supports both production and SDK builds.
- Caches builds to avoid unnecessary downloads.
- Minimal external dependencies: [tar](https://www.npmjs.com/package/tar) and [JSZip](https://www.npmjs.com/package/jszip).

## Installation
```bash
npm install @kogs/nwjs -g
```

## Usage
```bash
Usage: nwjs [options]

Options:
  --target-dir          Specify a target directory to install to.
  --version             Specify a version to install (e.g 0.49.2)
  --sdk                 Install the SDK flavor instead of the normal flavor.
  --no-cache            Disable caching of downloaded builds.
  --platform <string>   Override the platform to install for.
  --arch <string>       Override the architecture to install for.
  --download-server     Override the default download server to use.
```

## Documentation

- [Versions](#versions) - Specify a specific version of nw.js to install.
- [Target Directory](#target-directory) - Specify a target directory to install to.
- [Platform / Architecture](#platform--architecture) - Override the platform and architecture to install for.
- [Caching](#caching) - Cache downloaded builds to avoid unnecessary downloads.
- [Development Build (SDK)](#development-build-sdk) - Install the SDK flavor instead of the normal flavor.
- [Download Server](#download-server) - Override the default download server to use.
- [Locales](#locale) - Automatically remove locale files that you won't use.

### Verions

Using the `--version <version>` option you can specify a specific version of nw.js to install. This should be a valid version number such as `0.49.2` or `0.48.0-beta1`.

```bash
nwjs --version 0.48.0-beta1
```
If no version is specified, the latest stable version will be installed. This is determined by querying the directory listing of the download server.

```bash
nwjs # No version, latest is installed.
```

### Target Directory

By default, nw.js will be installed in the current working directory. To specify your own target directory, use the `--target-dir` option.

```bash
nwjs --target-dir /path/to/target # Installs to /path/to/target.
```
Keep in mind that files will be overwritten if they already exist in the target directory.

### Platform / Architecture

By default, the platform and architecture of the current system will be used. If you wish to override this, use the `--platform` and `--arch` options.

```bash
nwjs --platform win --arch x64 # Installs latest stable build for Windows x64.
```

> *Note*: For compatibility with the Node.js API, the platform `win32` is treated as `win` and the platform `darwin` is treated as `osx`.

At the time of writing, the following platforms and architectures are supported:
| Platform | Architecture  |
| -------- | ------------- |
| `win`    | `x64`  `ia32` |
| `linux`  | `x64`  `ia32` |
| `osx`    | `x64`         |

This utility does not validate against this table, and will attempt to download the build regardless of the platform or architecture specified. If the build does not exist, the download will fail.

### Caching

By default, downloaded builds are cached to the operating system's temporary directory. Installing the same version multiple times will re-use the cached build, avoiding unnecessary downloads.

```js
Path: os.tmpdir() + '/kogs-nwjs-cache/' + package
```

To disable this behavior, use the `--no-cache` option. The cache will not be checked or updated when this option is used.

```bash
nwjs --no-cache # Disables caching.
```

### Development Build (SDK)

By default, the normal flavor of nw.js will be installed. If you wish to install the SDK flavor instead, use the `--sdk` option.

```bash
nwjs --sdk # Installs latest stable SDK build.
```

### Download Server

By default, the utility will use the [official download server](https://dl.nwjs.io/) when downloading builds. If you wish to use a different server, use the `--download-server` option.

```bash
nwjs --download-server https://example.com/
```

Notes on custom download servers:
- The server is expecting an Apache-style directory listing.
- Builds are scraped using `/<a href="v([^"]+)\/">v[^<]+\/<\/a>/g`
- Versions must match `/^(\d+)\.(\d+)\.(\d+)(-[a-z0-9]+)?$/`

### Locale

By default, builds come with locale files for [all languages supported by Chromium](https://chromium.googlesource.com/chromium/src/build/config/+/refs/heads/main/locales.gni). According to the [developer of nw.js](https://github.com/nwjs/nw.js/issues/2244#issuecomment-379977958), it is safe to remove locale files that you won't be using.

`@kogs/nwjs` makes this simple with the `--locale <locales>` option, where `<locales>` is a comma-separated list of locales to include.

```bash
nwjs --locale "sw,en-GB,en_US"
```

Notes on locale:
- You should use the locale IDs that Chromium uses, not platform-specific variations. For example, don't use `en` (OSX variation) for `en-US`, `@kogs/nwjs` will automatically adjust this when building for OSX.
- The locale files **are** not related to the language content of your application, it is related to the environment in which the application will be deployed. **Do not** exclude locale files unless you are 100% sure of the locale of the computers your application will be deployed onto.
- The use of hyphens and underscores differs between platforms, as such you can use either and `@kogs/nwjs` will automatically adjust them depending on the target platform (e.g `en-gb` == `en_gb`).
- Locale flags are case-insensitive (e.g `en-GB` == `en-gb`).

## What is `@kogs`?
`@kogs` is a collection of packages that I've written to consolidate the code I often reuse across my projects with the following goals in mind:

- Consistent API.
- Minimal dependencies.
- Full TypeScript definitions.
- Avoid feature creep.
- ES6+ syntax.

All of the packages in the `@kogs` collection can be found [on npm under the `@kogs` scope.](https://www.npmjs.com/settings/kogs/packages)

## Contributing / Feedback / Issues
Feedback, bug reports and contributions are welcome. Please use the [GitHub issue tracker](https://github.com/Kruithne/kogs-nwjs/issues) and follow the guidelines found in the [CONTRIBUTING](CONTRIBUTING.md) file.

## License
The code in this repository is licensed under the ISC license. See the [LICENSE](LICENSE) file for more information.