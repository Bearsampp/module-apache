# Gradle Documentation

This directory contains all Gradle-related documentation for the module-apache project.

## Documentation Files

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

## Recent Updates

### Remote apache.properties Support
The Gradle build now supports fetching Apache versions from the remote apache.properties file hosted at:
https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties

**Version Resolution Priority:**
1. Local `releases.properties` file
2. Remote `apache.properties` from modules-untouched
3. Direct download from modules-untouched repository (apache{version}/ directory)

This ensures that versions not yet added to the local releases.properties can still be built automatically.

### Documentation Organization
All Gradle-related documentation has been moved to `/.gradle-docs` to keep the project root clean and organized.

## Quick Links

- [Gradle Updates](GRADLE_UPDATES.md) - Full changelog and feature documentation
- [Changes Summary](CHANGES_SUMMARY.md) - Quick overview of changes
- [Tmp Paths Documentation](GRADLE_TMP_PATHS.md) - Build path structure details
- [Tmp Paths Changes](CHANGES_TMP_PATHS.md) - Path configuration updates

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
