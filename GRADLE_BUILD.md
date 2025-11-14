# Gradle Build System

This project uses **Gradle** as its build system. This document provides a quick reference for building Apache module packages.

## Quick Start

```bash
# Display build information
gradle info

# List available versions
gradle listVersions

# Build a specific version
gradle release -PbundleVersion=2.4.62

# Build all versions
gradle releaseAll

# Verify build environment
gradle verify
```

## Prerequisites

| Requirement                      | Version/Details                                                              |
|----------------------------------|------------------------------------------------------------------------------|
| Java                             | Java 8 or higher                                                             |
| Gradle                           | 6.0+ (or use included wrapper)                                               |
| 7-Zip                            | Required for .7z format (optional for .zip)                                  |

## Available Commands

| Command                                      | Description                                  |
|----------------------------------------------|----------------------------------------------|
| `gradle info`                                | Display build configuration                  |
| `gradle listVersions`                        | List all available versions                  |
| `gradle release -PbundleVersion=2.4.62`      | Build specific version                       |
| `gradle release`                             | Interactive version selection                |
| `gradle releaseAll`                          | Build all available versions                 |
| `gradle verify`                              | Verify build environment                     |
| `gradle clean`                               | Clean build artifacts                        |
| `gradle tasks`                               | List all available tasks                     |

## Configuration

### build.properties

Configure the module build settings:

```properties
bundle.name    = apache
bundle.release = 2025.8.15
bundle.type    = bins
bundle.format  = 7z
```

### Build Path

Configure the build output path (priority order):

| Priority | Method                          | Example                                                      |
|----------|---------------------------------|--------------------------------------------------------------|
| 1        | In `build.properties`           | `build.path = C:/Bearsampp-build`                            |
| 2        | Environment variable            | `set BEARSAMPP_BUILD_PATH=C:/Bearsampp-build`                |
| 3        | Default                         | Uses `../bearsampp-build` relative to project root           |

## Output Structure

Build artifacts are created in:

```
bearsampp-build/
├── bins/
│   └── apache/
│       └── {release}/
│           ├── bearsampp-apache-{version}-{release}.7z
│           ├── bearsampp-apache-{version}-{release}.7z.md5
│           ├── bearsampp-apache-{version}-{release}.7z.sha1
│           ├── bearsampp-apache-{version}-{release}.7z.sha256
│           └── bearsampp-apache-{version}-{release}.7z.sha512
└── tmp/
    ├── bundles_build/bins/apache/
    ├── bundles_prep/bins/apache/
    ├── bundles_src/
    ├── downloads/apache/
    └── extract/apache/
```

## Features

### Automatic Version Discovery

The build system automatically discovers Apache versions from:

| Priority | Source                                    | Description                                  |
|----------|-------------------------------------------|----------------------------------------------|
| 1        | Local `bin/` directory                    | Current/active versions                      |
| 2        | Local `releases.properties`               | Local version definitions                    |
| 3        | Remote `apache.properties`                | From modules-untouched repository            |
| 4        | Direct repository download                | From `apache{version}/` directory            |

### Interactive Mode

Run `gradle release` without parameters for interactive version selection:

```bash
gradle release
```

The system will display all available versions and prompt you to select one.

### Batch Building

Build all available versions at once:

```bash
gradle releaseAll
```

## Documentation

Comprehensive documentation is available in the `/.gradle-docs` directory:

| Document                                                          | Description                                    |
|-------------------------------------------------------------------|------------------------------------------------|
| [README.md](.gradle-docs/README.md)                               | Documentation index and quick reference        |
| [GRADLE_UPDATES.md](.gradle-docs/GRADLE_UPDATES.md)              | Complete build system documentation            |
| [CHANGES_SUMMARY.md](.gradle-docs/CHANGES_SUMMARY.md)            | Summary of all changes and updates             |
| [GRADLE_TMP_PATHS.md](.gradle-docs/GRADLE_TMP_PATHS.md)          | Build path structure and configuration         |
| [REMOTE_PROPERTIES_SUPPORT.md](.gradle-docs/REMOTE_PROPERTIES_SUPPORT.md) | Remote version discovery documentation |
| [CHANGELOG.md](.gradle-docs/CHANGELOG.md)                         | Complete changelog of all updates              |
| [GRADLE_CONVERSION_COMPLETE.md](.gradle-docs/GRADLE_CONVERSION_COMPLETE.md) | Conversion summary and status |

## Troubleshooting

### 7-Zip Not Found

If you get a "7-Zip not found" error:

1. Install 7-Zip from https://www.7-zip.org/
2. Or set the `7Z_HOME` environment variable:
   ```bash
   set 7Z_HOME=D:\Tools\7-Zip
   ```

### Version Not Found

If a version is not found:

1. Check if it exists in `bin/` or `bin/archived/` directories
2. Add it to `releases.properties` with a download URL
3. Or add it to the remote `apache.properties` in modules-untouched repository

### Build Path Issues

If you need to change the build output location:

1. Edit `build.properties` and add: `build.path = C:/Your/Path`
2. Or set environment variable: `set BEARSAMPP_BUILD_PATH=C:/Your/Path`

## Support

- **Issues:** https://github.com/bearsampp/bearsampp/issues
- **Documentation:** `/.gradle-docs` directory
- **Main Project:** https://github.com/bearsampp/bearsampp

## License

This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.
