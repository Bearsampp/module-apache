# Modules-Untouched Integration

## Overview

The module-apache build system is fully integrated with the [modules-untouched](https://github.com/Bearsampp/modules-untouched) repository for centralized version management. This document explains how the integration works and how to use it.

## Key Changes

### Removal of releases.properties

As of the latest update, the local `releases.properties` file has been **removed**. All version information is now managed centrally in the modules-untouched repository.

| Before                           | After                                                                        |
|----------------------------------|------------------------------------------------------------------------------|
| Local `releases.properties` file | Remote `apache.properties` from modules-untouched                            |
| Manual version updates required  | Automatic version discovery                                                  |
| Version info could be outdated   | Always current with repository                                               |
| Inconsistent across modules      | Consistent with module-bruno and others                                      |

## Version Resolution

### Resolution Strategy

The build system uses a two-tier approach to resolve Apache versions:

| Priority | Source                                    | Description                                  | When Used                    |
|----------|-------------------------------------------|----------------------------------------------|------------------------------|
| 1        | Remote `apache.properties`                | Fetch from modules-untouched repository      | Always tried first           |
| 2        | Direct repository archive                 | Check for archive files in repository        | Fallback if properties fail  |

### How It Works

```
Build Request (gradle release -PbundleVersion=2.4.62)
    ↓
Check bin/ directory for existing binaries
    ↓ (not found)
Fetch apache.properties from modules-untouched
    ↓
Look up version 2.4.62 in properties
    ↓ (found)
Download archive from URL specified in properties
    ↓
Extract and prepare Apache binaries
    ↓
Build release package
```

## Integration Functions

### fetchModulesUntouchedProperties()

Primary function for fetching version information:

```groovy
def fetchModulesUntouchedProperties() {
    def propsUrl = "https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/apache.properties"

    try {
        def connection = new URL(propsUrl).openConnection()
        connection.setConnectTimeout(10000)
        connection.setReadTimeout(10000)

        def props = new Properties()
        connection.inputStream.withStream { stream ->
            props.load(stream)
        }

        return props
    } catch (Exception e) {
        println "  Warning: Could not fetch modules-untouched apache.properties: ${e.message}"
        return null
    }
}
```

**Features:**
- 10-second connection timeout
- 10-second read timeout
- Graceful error handling
- Returns null on failure (triggers fallback)

### downloadFromModulesUntouched()

Downloads and extracts Apache binaries from modules-untouched:

```groovy
def downloadFromModulesUntouched(String version) {
    println "Checking modules-untouched repository on GitHub..."

    // First, try to get the URL from apache.properties
    def untouchedProps = fetchModulesUntouchedProperties()
    def downloadUrl = untouchedProps?.getProperty(version)

    if (downloadUrl) {
        println "  Found version ${version} in modules-untouched apache.properties"
        println "  URL: ${downloadUrl}"
        
        // Download and extract the archive
        // ...
    } else {
        // Fallback: Try to detect the archive file by checking common patterns
        // ...
    }
}
```

**Features:**
- Checks remote properties first
- Falls back to direct archive detection
- Supports both .7z and .zip formats
- Caches downloaded files
- Auto-detects Apache directory structure

## Benefits

### Centralized Management

| Benefit                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Single Source of Truth           | All Apache versions defined in one place                                     |
| Consistent Across Modules        | Same approach as module-bruno, module-php, etc.                              |
| Easy Updates                     | Add version once, available to all builds                                    |
| No Local Maintenance             | No need to maintain local version files                                      |

### Automatic Discovery

| Benefit                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Always Current                   | Automatically uses latest version information                                |
| No Manual Updates                | New versions available immediately after adding to repository                |
| Reduced Errors                   | No risk of outdated or incorrect local version info                          |
| Simplified Workflow              | One less file to maintain and sync                                           |

### Reliability

| Benefit                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Fallback Mechanisms              | Multiple ways to obtain Apache binaries                                      |
| Network Resilience               | Graceful handling of network errors                                          |
| Clear Error Messages             | Helpful guidance when versions not found                                     |
| Cached Downloads                 | Reuses previously downloaded files                                           |

## Usage

### Building a Version

Simply specify the version you want to build:

```bash
gradle release -PbundleVersion=2.4.62
```

The build system will:
1. Check if binaries exist in `bin/apache2.4.62/`
2. If not, fetch apache.properties from modules-untouched
3. Look up version 2.4.62 in the properties
4. Download and extract the Apache binaries
5. Build the release package

### Listing Available Versions

View all versions available in modules-untouched:

```bash
gradle listReleases
```

**Output:**
```
Available Apache Releases (modules-untouched):
--------------------------------------------------------------------------------
  2.4.41     -> https://github.com/Bearsampp/modules-untouched/releases/...
  2.4.51     -> https://github.com/Bearsampp/modules-untouched/releases/...
  2.4.52     -> https://github.com/Bearsampp/modules-untouched/releases/...
  ...
--------------------------------------------------------------------------------
Total releases: 15
```

### Checking Integration

Verify the modules-untouched integration:

```bash
gradle checkModulesUntouched
```

**Output:**
```
======================================================================
Modules-Untouched Integration Check
======================================================================

Repository URL:
  https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/apache.properties

Fetching apache.properties from modules-untouched...

======================================================================
Available Versions in modules-untouched
======================================================================
  2.4.41
  2.4.51
  2.4.52
  ...
======================================================================
Total versions: 15

======================================================================
[SUCCESS] modules-untouched integration is working
======================================================================

Version Resolution Strategy:
  1. Check modules-untouched apache.properties (remote)
  2. Check GitHub repository for direct archive files (fallback)
```

## Adding New Versions

### Method 1: Update apache.properties (Recommended)

This is the recommended method as it makes the version immediately available to all builds.

**Steps:**

1. Navigate to the modules-untouched repository:
   ```
   https://github.com/Bearsampp/modules-untouched
   ```

2. Edit `modules/apache.properties`:
   ```
   https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties
   ```

3. Add a new line for your version:
   ```properties
   2.4.66 = https://github.com/Bearsampp/modules-untouched/releases/download/apache-2.4.66/bearsampp-apache-2.4.66.7z
   ```

4. Commit and push the changes

5. The version is now immediately available to all builds

**Benefits:**
- Immediate availability
- No additional files needed
- Centralized management
- Consistent with other modules

### Method 2: Upload Archive to Repository

Upload Apache binaries directly to the repository for auto-discovery.

**Steps:**

1. Create a directory in modules-untouched:
   ```
   apache2.4.66/
   ```

2. Upload your Apache archive:
   ```
   apache2.4.66/bearsampp-apache-2.4.66.7z
   ```
   or
   ```
   apache2.4.66/apache-2.4.66.7z
   ```

3. The build system will auto-detect the archive

**Benefits:**
- No properties file editing needed
- Archive is stored in repository
- Auto-discovery works automatically

**Note:** Method 1 is preferred as it provides better control and documentation.

## Error Handling

### Network Errors

If the remote properties cannot be fetched:

```
Warning: Could not fetch modules-untouched apache.properties: Connection timeout
Apache 2.4.66 not found in modules-untouched repository
```

**Resolution:**
- Check your internet connection
- Verify the modules-untouched repository is accessible
- Try again later if repository is temporarily unavailable

### Version Not Found

If a version is not found in any location:

```
Failed to obtain Apache binaries for version 2.4.66

You can manually:
1. Download and extract Apache binaries to: bin/apache2.4.66/Apache24/
2. Add version 2.4.66 to https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties
3. Upload Apache binaries to: https://github.com/Bearsampp/modules-untouched/tree/main/apache2.4.66/
```

**Resolution:**
- Add the version to apache.properties (Method 1)
- Upload binaries to repository (Method 2)
- Manually place binaries in bin/ directory (temporary solution)

## Configuration

### Timeout Settings

The integration uses these timeout settings:

| Setting                          | Value      | Purpose                                      |
|----------------------------------|------------|----------------------------------------------|
| Connect Timeout                  | 10 seconds | Time to establish connection                 |
| Read Timeout                     | 10 seconds | Time to read data from connection            |

These can be adjusted in `fetchModulesUntouchedProperties()` if needed.

### Cache Directory

Downloaded files are cached in:
```
{buildBasePath}/tmp/downloads/apache/
```

This prevents re-downloading the same version multiple times.

## Comparison with Other Modules

### module-bruno

Module-apache now uses the same integration pattern as module-bruno:

| Aspect                           | module-bruno                                 | module-apache                                |
|----------------------------------|----------------------------------------------|----------------------------------------------|
| Version Source                   | Remote bruno.properties                      | Remote apache.properties                     |
| Local Properties File            | None                                         | None (removed)                               |
| Fallback Mechanism               | Direct archive detection                     | Direct archive detection                     |
| Integration Function             | `fetchModulesUntouchedProperties()`          | `fetchModulesUntouchedProperties()`          |
| Download Function                | `downloadFromModulesUntouched()`             | `downloadFromModulesUntouched()`             |

### Consistency Benefits

| Benefit                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Unified Approach                 | All modules use the same pattern                                             |
| Easier Maintenance               | Same code structure across modules                                           |
| Shared Knowledge                 | Developers familiar with one module understand others                        |
| Consistent Behavior              | All modules behave the same way                                              |

## Testing

### Test Scenarios

| Test                             | Command                                      | Expected Result                              |
|----------------------------------|----------------------------------------------|----------------------------------------------|
| Build existing version           | `gradle release -PbundleVersion=2.4.62`      | Fetches from remote, builds successfully     |
| Build new version                | `gradle release -PbundleVersion=2.4.66`      | Fetches from remote, builds successfully     |
| List remote versions             | `gradle listReleases`                        | Shows all versions from apache.properties    |
| Check integration                | `gradle checkModulesUntouched`               | Verifies connection and lists versions       |
| Network error handling           | Disconnect network, build version            | Shows appropriate error message              |
| Fallback mechanism               | Build version not in properties              | Tries direct archive detection               |

### Verification Steps

1. **Test remote fetch:**
   ```bash
   gradle checkModulesUntouched
   ```
   Should show all available versions.

2. **Test version build:**
   ```bash
   gradle release -PbundleVersion=2.4.62
   ```
   Should fetch from remote and build successfully.

3. **Test error handling:**
   ```bash
   gradle release -PbundleVersion=9.9.99
   ```
   Should show helpful error message.

## Migration from releases.properties

### What Changed

| Before                           | After                                                                        |
|----------------------------------|------------------------------------------------------------------------------|
| `releases.properties` file       | Removed                                                                      |
| Local version management         | Remote version management                                                    |
| Manual updates required          | Automatic updates                                                            |
| Version info in local file       | Version info in modules-untouched                                            |

### Migration Steps

**No action required!** The migration is automatic:

1. The `releases.properties` file has been removed
2. The build system now uses remote apache.properties
3. All previously available versions remain accessible
4. New versions are automatically available when added to repository

### Backward Compatibility

- All existing build commands work the same way
- All existing versions remain available
- No changes to build output or structure
- No changes to task names or behavior

## Troubleshooting

### Common Issues

| Issue                            | Cause                                        | Solution                                     |
|----------------------------------|----------------------------------------------|----------------------------------------------|
| Cannot fetch properties          | Network connectivity                         | Check internet connection                    |
| Version not found                | Version not in repository                    | Add to apache.properties or upload binaries  |
| Download fails                   | Invalid URL in properties                    | Update URL in apache.properties              |
| Extraction fails                 | Corrupted archive                            | Re-upload archive to repository              |

### Debug Mode

For detailed output, run with `--info` flag:

```bash
gradle release -PbundleVersion=2.4.62 --info
```

This shows:
- Connection attempts
- Property file contents
- Download progress
- Extraction details
- All build steps

## Related Documentation

| Document                         | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| [REMOTE_PROPERTIES_SUPPORT.md](REMOTE_PROPERTIES_SUPPORT.md) | Detailed remote properties documentation        |
| [CHANGELOG.md](CHANGELOG.md)     | Version history including removal of releases.properties                     |
| [README.md](README.md)           | Documentation index                                                          |
| [GRADLE_UPDATES.md](GRADLE_UPDATES.md) | Complete build system documentation                                    |

## External Links

| Resource                         | URL                                                                          |
|----------------------------------|------------------------------------------------------------------------------|
| modules-untouched repository     | https://github.com/Bearsampp/modules-untouched                               |
| apache.properties                | https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties |
| Apache releases                  | https://github.com/Bearsampp/modules-untouched/releases                      |

## Summary

The modules-untouched integration provides:

✅ **Centralized version management** - Single source of truth  
✅ **Automatic version discovery** - No manual updates needed  
✅ **Consistent across modules** - Same pattern as module-bruno  
✅ **Reliable fallback mechanisms** - Multiple ways to obtain binaries  
✅ **Clear error messages** - Helpful guidance when issues occur  
✅ **No local maintenance** - No releases.properties to maintain  

This integration simplifies the build process and ensures all Bearsampp modules use a consistent, maintainable approach to version management.
