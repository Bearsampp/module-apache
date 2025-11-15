# Gradle Build Changelog

## 2025-01-XX - Removal of releases.properties

### Removed

| Component                        | Reason                                                                       |
|----------------------------------|------------------------------------------------------------------------------|
| `releases.properties`            | Replaced by centralized modules-untouched apache.properties                  |

### Changed

| Component                        | Change                                                                       |
|----------------------------------|------------------------------------------------------------------------------|
| Version Resolution               | Now uses only remote apache.properties (no local fallback)                   |
| `fetchModulesUntouchedProperties()` | Primary version source (was secondary)                                    |
| `downloadFromModulesUntouched()` | Enhanced to check remote properties first                                    |
| `listReleases` task              | Now fetches from modules-untouched instead of local file                     |
| Documentation                    | Updated to reflect removal of releases.properties                            |

### Version Resolution Strategy (Updated)

| Priority | Source                                    | Description                                  |
|----------|-------------------------------------------|----------------------------------------------|
| 1        | Remote `apache.properties`                | Fetch from modules-untouched (PRIMARY)       |
| 2        | Direct repository download                | Download from `apache{version}/` directory   |

**Note:** Local `releases.properties` is no longer used.

### Benefits

| Benefit                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Centralized Management           | Single source of truth for all Apache versions                               |
| No Local Maintenance             | No need to maintain local releases.properties file                           |
| Consistency                      | Matches module-bruno and other Bearsampp modules                             |
| Always Current                   | Automatically uses latest version information                                |
| Simplified Workflow              | One less file to maintain and keep in sync                                   |

### Migration Notes

- **No action required** for existing builds
- Version information now fetched dynamically from modules-untouched
- All previously available versions remain accessible
- New versions automatically available when added to remote apache.properties

### Files Modified

| File/Directory                   | Action  | Description                                                  |
|----------------------------------|---------|--------------------------------------------------------------|
| `releases.properties`            | Deleted | No longer needed                                             |
| `build.gradle`                   | Updated | Removed references to releases.properties                    |
| `/.gradle-docs/REMOTE_PROPERTIES_SUPPORT.md` | Updated | Reflects removal of releases.properties          |
| `/.gradle-docs/CHANGELOG.md`     | Updated | This changelog entry                                         |

### Testing

| Test                             | Status | Description                                                  |
|----------------------------------|--------|--------------------------------------------------------------|
| Build with remote version        | ✅     | Successfully fetches from modules-untouched                  |
| Build without network            | ✅     | Shows appropriate error message                              |
| `listReleases` task              | ✅     | Displays versions from remote apache.properties              |
| `checkModulesUntouched` task     | ✅     | Verifies integration with modules-untouched                  |

---

## 2024-11-12 - Remote Properties Support & Documentation Organization

### Added

| Feature                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Remote apache.properties Support | Fetches version info from modules-untouched repository                       |
| `loadRemoteApacheProperties()`   | New function to load and parse remote properties file                        |
| Documentation Directory          | Created `/.gradle-docs` for all Gradle documentation                         |
| REMOTE_PROPERTIES_SUPPORT.md     | Comprehensive guide for remote properties feature                            |

### Changed

| Component                        | Change                                                                       |
|----------------------------------|------------------------------------------------------------------------------|
| Version Resolution               | Updated `downloadAndExtractApache()` to check remote properties              |
| Error Messages                   | Enhanced to include all three version resolution options                     |
| Documentation Location           | Moved all Gradle docs to `/.gradle-docs/`                                    |

### Version Resolution Priority

| Priority | Source                                    | Description                                  |
|----------|-------------------------------------------|----------------------------------------------|
| 1        | Local `releases.properties`               | Check local file first                       |
| 2        | Remote `apache.properties`                | Fetch from modules-untouched (NEW)           |
| 3        | Direct repository download                | Download from `apache{version}/` directory   |

### Benefits

| Benefit                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Automatic Version Discovery      | No need to manually update local `releases.properties`                       |
| Centralized Management           | All versions maintained in modules-untouched repository                      |
| Better Organization              | All Gradle documentation in one dedicated directory                          |
| Improved Maintainability         | Easier to find and update documentation                                      |

### Files Modified

| File/Directory                   | Action  | Description                                                  |
|----------------------------------|---------|--------------------------------------------------------------|
| `build.gradle`                   | Updated | Added remote properties support                              |
| `/.gradle-docs/`                 | Created | New directory for all Gradle documentation                   |
| Documentation files              | Moved   | 4 files moved to `/.gradle-docs/`                            |
| `/.gradle-docs/README.md`        | Created | Documentation index                                          |
| `/.gradle-docs/REMOTE_PROPERTIES_SUPPORT.md` | Created | Remote properties guide                          |
| `/.gradle-docs/CHANGELOG.md`     | Created | This changelog file                                          |

### Testing

| Test                             | Status | Description                                                  |
|----------------------------------|--------|--------------------------------------------------------------|
| `gradle info`                    | ✅     | Displays build information correctly                         |
| Remote properties loading        | ✅     | Function works as expected                                   |
| Version resolution priority      | ✅     | Implemented correctly                                        |
| Error handling                   | ✅     | Network issues handled gracefully                            |
| Documentation migration          | ✅     | Files successfully moved                                     |

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

| Link                             | URL/Path                                                                     |
|----------------------------------|------------------------------------------------------------------------------|
| Remote Properties                | https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties |
| Documentation Index              | [/.gradle-docs/README.md](README.md)                                         |
| Remote Properties Guide          | [REMOTE_PROPERTIES_SUPPORT.md](REMOTE_PROPERTIES_SUPPORT.md)                 |

---

## Previous Changes

For previous changes, see:

| Document                         | Content                                                                      |
|----------------------------------|------------------------------------------------------------------------------|
| [GRADLE_UPDATES.md](GRADLE_UPDATES.md) | Build path configuration, 7-Zip detection, version management      |
| [CHANGES_SUMMARY.md](CHANGES_SUMMARY.md) | Summary of initial Gradle conversion                             |
| [GRADLE_TMP_PATHS.md](GRADLE_TMP_PATHS.md) | Temporary paths structure                                      |
| [CHANGES_TMP_PATHS.md](CHANGES_TMP_PATHS.md) | Temporary paths changes                                      |
