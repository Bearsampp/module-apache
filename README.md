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

## Documentation

**For detailed build instructions and documentation, see [.gradle-docs/README.md](.gradle-docs/README.md)**

Additional documentation:

| Document                                                          | Description                                    |
|-------------------------------------------------------------------|------------------------------------------------|
| [README.md](.gradle-docs/README.md)                               | Complete build documentation and guide         |
| [GRADLE_UPDATES.md](.gradle-docs/GRADLE_UPDATES.md)              | Detailed build system updates                  |
| [CHANGES_SUMMARY.md](.gradle-docs/CHANGES_SUMMARY.md)            | Summary of all changes                         |
| [GRADLE_TMP_PATHS.md](.gradle-docs/GRADLE_TMP_PATHS.md)          | Build path structure details                   |
| [CHANGES_TMP_PATHS.md](.gradle-docs/CHANGES_TMP_PATHS.md)        | Temporary paths configuration                  |
| [REMOTE_PROPERTIES_SUPPORT.md](.gradle-docs/REMOTE_PROPERTIES_SUPPORT.md) | Remote version discovery      |
| [MODULES_UNTOUCHED_INTEGRATION.md](.gradle-docs/MODULES_UNTOUCHED_INTEGRATION.md) | Integration guide     |
| [CHANGELOG.md](.gradle-docs/CHANGELOG.md)                         | Complete changelog                             |

## Configuration

### build.properties

Configure the module build settings:

```properties
bundle.name    = apache
bundle.release = 2025.8.15
bundle.type    = bins
bundle.format  = 7z
```

| Property          | Description                          | Example Value  |
|-------------------|--------------------------------------|----------------|
| `bundle.name`     | Name of the bundle                   | `apache`       |
| `bundle.release`  | Release version                      | `2025.8.15`    |
| `bundle.type`     | Type of bundle                       | `bins`         |
| `bundle.format`   | Archive format (7z or zip)           | `7z`           |

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
- `modules-untouched` - Remote repository (automatic discovery)

### Version Resolution Strategy

When building, the system checks for Apache binaries in this order:

1. **Local `bin/` directory** - Check for version folder
2. **Local `bin/archived/` directory** - Check archived versions
3. **Remote `apache.properties`** - Fetch from modules-untouched repository
4. **Direct repository download** - Download from modules-untouched GitHub

This multi-tier fallback strategy ensures maximum flexibility and automatic version discovery without manual configuration.

**Example:**
```bash
# List local versions
gradle listVersions

# List remote versions
gradle listReleases

# Check integration
gradle checkModulesUntouched
```

## Output Structure

Build artifacts are created in the `bearsampp-build` directory:

```
bearsampp-build/
├── bins/apache/{release}/                  # Final packaged archives
│   ├── bearsampp-apache-{version}-{release}.7z
│   ├── bearsampp-apache-{version}-{release}.7z.md5
│   ├── bearsampp-apache-{version}-{release}.7z.sha1
│   ├── bearsampp-apache-{version}-{release}.7z.sha256
│   └── bearsampp-apache-{version}-{release}.7z.sha512
└── tmp/                                    # Temporary build files
    ├── bundles_prep/bins/apache/           # Prepared bundles
    ├── bundles_build/bins/apache/          # Build staging
    ├── bundles_src/                        # Source bundles
    ├── downloads/apache/                   # Downloaded dependencies
    └── extract/apache/                     # Extracted archives
```

**Archive Structure:**
Each archive contains the Apache version folder at the root:
```
bearsampp-apache-2.4.62-2025.8.15.7z
└── apache2.4.62/              ← Version folder at root
    ├── bin/
    ├── conf/
    ├── modules/
    └── ...
```

## Features

### Interactive Mode

Run `gradle release` without parameters for interactive version selection:

```bash
gradle release
```

The system will display all available versions with location tags `[bin]` or `[bin/archived]` and prompt you to select one.

### Batch Building

Build all available versions at once:

```bash
gradle releaseAll
```

This will iterate through all versions in `bin/` and `bin/archived/` directories and build each one.

### Remote Version Discovery

The build system automatically discovers new Apache versions from the modules-untouched repository:

- Fetches version information from `apache.properties`
- Downloads binaries automatically when not found locally
- No manual configuration required for new versions
- Fallback to direct repository access if needed

**Check integration:**
```bash
gradle checkModulesUntouched
```

## Downloads

Official module downloads are available at:

**https://bearsampp.com/module/apache**

## Issues

Issues must be reported on [Bearsampp repository](https://github.com/bearsampp/bearsampp/issues).

## Contributing

Contributions are welcome! Please see the [Bearsampp contribution guidelines](https://bearsampp.com/contribute).

## Statistics

![Alt](https://repobeats.axiom.co/api/embed/9382037dc5be402bf4d32075c26f836b80ba253c.svg "Repobeats analytics image")

## License

This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.
