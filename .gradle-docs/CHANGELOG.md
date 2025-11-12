# Gradle Build Changelog

## 2024-11-12 - Remote Properties Support & Documentation Organization

### Added
- **Remote apache.properties Support**: Gradle now fetches version information from `https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties` when versions are not found in local `releases.properties`
- **New Function**: `loadRemoteApacheProperties()` - Loads and parses remote properties file
- **Documentation Directory**: Created `/.gradle-docs` to organize all Gradle-related documentation
- **New Documentation**: `REMOTE_PROPERTIES_SUPPORT.md` - Comprehensive guide for remote properties feature

### Changed
- **Version Resolution**: Updated `downloadAndExtractApache()` to check remote properties before falling back to direct repository download
- **Error Messages**: Enhanced error messages to include all three version resolution options
- **Documentation Location**: Moved all Gradle documentation files to `/.gradle-docs/`:
  - `GRADLE_UPDATES.md`
  - `CHANGES_SUMMARY.md`
  - `GRADLE_TMP_PATHS.md`
  - `CHANGES_TMP_PATHS.md`

### Version Resolution Priority
1. Local `releases.properties` file
2. Remote `apache.properties` from modules-untouched (NEW)
3. Direct download from modules-untouched repository (`apache{version}/` directory)

### Benefits
- **Automatic Version Discovery**: No need to manually update local `releases.properties` for every new version
- **Centralized Management**: All versions maintained in modules-untouched repository
- **Better Organization**: All Gradle documentation in one dedicated directory
- **Improved Maintainability**: Easier to find and update documentation

### Files Modified
- `build.gradle` - Added remote properties support
- Created `/.gradle-docs/` directory
- Moved 4 documentation files to `/.gradle-docs/`
- Created `/.gradle-docs/README.md`
- Created `/.gradle-docs/REMOTE_PROPERTIES_SUPPORT.md`
- Created `/.gradle-docs/CHANGELOG.md`

### Testing
- ✅ `gradle info` - Displays build information correctly
- ✅ Remote properties loading function works
- ✅ Version resolution priority implemented correctly
- ✅ Error handling for network issues
- ✅ Documentation files successfully moved

### Usage Example
```bash
# Build a version that's only in remote apache.properties
gradle release -PbundleVersion=2.4.XX

# Output will show:
# Version 2.4.XX not found in releases.properties
# Checking remote apache.properties from modules-untouched...
# Loading remote apache.properties from modules-untouched...
#   Loaded N versions from remote apache.properties
#   Found version 2.4.XX in remote apache.properties
#   URL: https://...
```

### Related Links
- Remote Properties: https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties
- Documentation: [/.gradle-docs/README.md](README.md)
- Remote Properties Guide: [REMOTE_PROPERTIES_SUPPORT.md](REMOTE_PROPERTIES_SUPPORT.md)

---

## Previous Changes

For previous changes, see:
- [GRADLE_UPDATES.md](GRADLE_UPDATES.md) - Build path configuration, 7-Zip detection, version management
- [CHANGES_SUMMARY.md](CHANGES_SUMMARY.md) - Summary of initial Gradle conversion
- [GRADLE_TMP_PATHS.md](GRADLE_TMP_PATHS.md) - Temporary paths structure
- [CHANGES_TMP_PATHS.md](CHANGES_TMP_PATHS.md) - Temporary paths changes
