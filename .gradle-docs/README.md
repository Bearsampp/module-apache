# Gradle Documentation

This directory contains all Gradle-related documentation for the module-apache project.

## Documentation Files

| Document                                                          | Description                                                                  |
|-------------------------------------------------------------------|------------------------------------------------------------------------------|
| [GRADLE_UPDATES.md](GRADLE_UPDATES.md)                            | Comprehensive documentation of all Gradle build updates                      |
| [CHANGES_SUMMARY.md](CHANGES_SUMMARY.md)                          | Summary of all changes made to the Gradle build configuration                |
| [GRADLE_TMP_PATHS.md](GRADLE_TMP_PATHS.md)                        | Documentation about the Gradle build temporary paths                         |
| [CHANGES_TMP_PATHS.md](CHANGES_TMP_PATHS.md)                      | Changes related to the Gradle temporary paths update                         |
| [REMOTE_PROPERTIES_SUPPORT.md](REMOTE_PROPERTIES_SUPPORT.md)      | Documentation for remote apache.properties support                           |
| [MODULES_UNTOUCHED_INTEGRATION.md](MODULES_UNTOUCHED_INTEGRATION.md) | Complete guide to modules-untouched integration                           |
| [CHANGELOG.md](CHANGELOG.md)                                      | Complete changelog of all Gradle build updates                               |

### GRADLE_UPDATES.md

Comprehensive documentation of all Gradle build updates, including:

- Build path configuration
- 7-Zip executable detection
- Version management
- Task improvements
- Usage examples

### CHANGES_SUMMARY.md

Summary of all changes made to the Gradle build configuration:

- Files modified
- Functional changes
- Task updates
- Behavior alignment with module-bruno
- Testing results

### GRADLE_TMP_PATHS.md

Documentation about the Gradle build temporary paths:

- Path structure comparison with Ant builds
- Path definitions and purposes
- How the build process works
- Example build flow
- Configuration options

### CHANGES_TMP_PATHS.md

Changes related to the Gradle temporary paths update:

- Path configuration changes
- Directory structure before/after
- Benefits of the new structure
- Verification steps

### REMOTE_PROPERTIES_SUPPORT.md

Documentation for remote apache.properties support:

- How the remote properties loading works
- Version resolution priority
- Usage examples and error handling
- Configuration and maintenance

### MODULES_UNTOUCHED_INTEGRATION.md

Complete guide to modules-untouched integration:

- Overview of the integration
- Removal of releases.properties
- Version resolution strategy
- Adding new versions
- Troubleshooting and testing

### CHANGELOG.md

Complete changelog of all Gradle build updates:

- Version history
- Feature additions
- Bug fixes
- Breaking changes

## Recent Updates

### Removal of releases.properties (2025-01-XX)
The local `releases.properties` file has been **removed**. The Gradle build now fetches all Apache version information directly from the remote apache.properties file hosted at:
https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties

**Version Resolution Strategy:**
1. Remote `apache.properties` from modules-untouched (PRIMARY)
2. Direct download from modules-untouched repository (apache{version}/ directory) (FALLBACK)

This provides centralized version management consistent with module-bruno and other Bearsampp modules.

### Remote apache.properties Support (2024-11-12)
The Gradle build supports fetching Apache versions from the remote apache.properties file, eliminating the need for local version management.

### Documentation Organization
All Gradle-related documentation has been moved to `/.gradle-docs` to keep the project root clean and organized.

## Quick Links

| Link                                                             | Description                                                                  |
|------------------------------------------------------------------|------------------------------------------------------------------------------|
| [Gradle Updates](GRADLE_UPDATES.md)                              | Full changelog and feature documentation                                     |
| [Changes Summary](CHANGES_SUMMARY.md)                            | Quick overview of changes                                                    |
| [Tmp Paths Documentation](GRADLE_TMP_PATHS.md)                   | Build path structure details                                                 |
| [Tmp Paths Changes](CHANGES_TMP_PATHS.md)                        | Path configuration updates                                                   |
| [Remote Properties Support](REMOTE_PROPERTIES_SUPPORT.md)        | Remote version discovery documentation                                       |
| [Modules-Untouched Integration](MODULES_UNTOUCHED_INTEGRATION.md) | Complete integration guide                                                  |
| [Changelog](CHANGELOG.md)                                        | Complete version history                                                     |

## Usage

For build instructions and task documentation, see [GRADLE_UPDATES.md](GRADLE_UPDATES.md).

For quick reference:
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
