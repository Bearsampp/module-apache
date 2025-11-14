<p align="center"><a href="https://bearsampp.com/contribute" target="_blank"><img width="250" src="img/Bearsampp-logo.svg"></a></p>

[![GitHub release](https://img.shields.io/github/release/bearsampp/module-apache.svg?style=flat-square)](https://github.com/bearsampp/module-apache/releases/latest)
![Total downloads](https://img.shields.io/github/downloads/bearsampp/module-apache/total.svg?style=flat-square)

# Bearsampp Module - Apache

This is a module of [Bearsampp project](https://github.com/bearsampp/bearsampp) involving Apache HTTP Server.

## Build System

This module uses **Gradle** as its build system. The Gradle build provides:

- ✅ Pure Gradle build (no Ant dependencies)
- ✅ Automatic Apache binary download and extraction
- ✅ Remote version discovery from modules-untouched repository
- ✅ Interactive and non-interactive build modes
- ✅ Support for archived versions
- ✅ Configurable build paths
- ✅ Hash file generation (MD5, SHA1, SHA256, SHA512)
- ✅ 7-Zip and ZIP archive formats

## Quick Start

### Prerequisites

- Java 8 or higher
- Gradle (or use the included wrapper)
- 7-Zip (for .7z format archives)

### Build Commands

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

# Clean build artifacts
gradle clean
```

**For detailed build instructions, see [GRADLE_BUILD.md](GRADLE_BUILD.md)**

## Documentation

All Gradle build documentation is located in the `/.gradle-docs` directory:

| Document                                                          | Description                                    |
|-------------------------------------------------------------------|------------------------------------------------|
| [README.md](.gradle-docs/README.md)                               | Documentation index and quick reference        |
| [GRADLE_UPDATES.md](.gradle-docs/GRADLE_UPDATES.md)              | Complete build system documentation            |
| [CHANGES_SUMMARY.md](.gradle-docs/CHANGES_SUMMARY.md)            | Summary of all changes and updates             |
| [GRADLE_TMP_PATHS.md](.gradle-docs/GRADLE_TMP_PATHS.md)          | Build path structure and configuration         |
| [CHANGES_TMP_PATHS.md](.gradle-docs/CHANGES_TMP_PATHS.md)        | Temporary paths update details                 |
| [REMOTE_PROPERTIES_SUPPORT.md](.gradle-docs/REMOTE_PROPERTIES_SUPPORT.md) | Remote version discovery documentation |
| [CHANGELOG.md](.gradle-docs/CHANGELOG.md)                         | Complete changelog of all updates              |

## Configuration

### build.properties

Configure the module build settings:

```properties
bundle.name    = apache
bundle.release = 2025.8.15
bundle.type    = bins
bundle.format  = 7z
```

### Build Path Configuration

You can configure the build output path in three ways (priority order):

1. **In build.properties:**
   ```properties
   build.path = C:/Bearsampp-build
   ```

2. **Environment variable:**
   ```bash
   set BEARSAMPP_BUILD_PATH=C:/Bearsampp-build
   ```

3. **Default:** Uses `../bearsampp-build` relative to project root

## Version Management

### Available Versions

Versions are detected from:
- `bin/` - Current/active versions
- `bin/archived/` - Archived/older versions

### Version Resolution

When building, the system checks for Apache binaries in this order:

1. Local `bin/` directory
2. Local `releases.properties` file
3. Remote `apache.properties` from modules-untouched repository
4. Direct download from modules-untouched repository

This ensures maximum flexibility and automatic version discovery.

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

### Remote Version Discovery

The build system automatically discovers new Apache versions from the modules-untouched repository, eliminating the need to manually update `releases.properties` for every new version.

## Module Downloads

Official module downloads are available at:

https://bearsampp.com/module/apache

## Issues

Issues must be reported on [Bearsampp repository](https://github.com/bearsampp/bearsampp/issues).

## Contributing

Contributions are welcome! Please see the [Bearsampp contribution guidelines](https://bearsampp.com/contribute).

## Statistics

![Alt](https://repobeats.axiom.co/api/embed/9382037dc5be402bf4d32075c26f836b80ba253c.svg "Repobeats analytics image")

## License

This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.
