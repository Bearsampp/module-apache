# Remote apache.properties Support

## Overview

The Gradle build system fetches Apache version information directly from the remote `apache.properties` file hosted in the modules-untouched repository. The local `releases.properties` file has been **removed** in favor of this centralized, dynamic approach.

## Remote Properties URL

```
https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/apache.properties
```

## Version Resolution Strategy

When building an Apache version, the system follows this resolution order:

| Priority | Source                                    | Description                                                  |
|----------|-------------------------------------------|--------------------------------------------------------------|
| 1        | Remote `apache.properties`                | Fetch from modules-untouched repository (PRIMARY)            |
| 2        | Direct repository download                | Download from `apache{version}/` directory (FALLBACK)        |

**Note:** The local `releases.properties` file has been removed. All version information is now managed centrally in the modules-untouched repository.

## How It Works

### Function: fetchModulesUntouchedProperties()

The primary helper function in `build.gradle`:

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

### Integration with downloadFromModulesUntouched()

The `downloadFromModulesUntouched()` function uses the remote properties:

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

## Benefits

| Benefit                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Centralized Version Management   | All versions maintained in one place (modules-untouched)                     |
| No Local Maintenance             | No need to maintain a local `releases.properties` file                       |
| Always Up-to-Date                | Automatically uses the latest version information from the repository        |
| Consistent Across Modules        | Same approach as module-bruno and other Bearsampp modules                    |
| Fallback Support                 | Multiple fallback mechanisms ensure builds can proceed                       |
| Network Resilience               | Graceful handling of network errors with clear error messages                |

## Usage Example

### Building Any Apache Version

```bash
# Build any version available in modules-untouched
gradle release -PbundleVersion=2.4.66
```

**Output:**
```
Apache binaries not found in bin/ directory
Checking modules-untouched repository...
Checking modules-untouched repository on GitHub...
  Found version 2.4.66 in modules-untouched apache.properties
  URL: https://github.com/Bearsampp/modules-untouched/releases/download/...
  Downloading from modules-untouched: https://...
  Download complete
  Extracting archive...
  Extraction complete
  Found Apache directory: Apache24
  Apache 2.4.66 from modules-untouched ready at: ...
```

## Error Handling

### Network Errors

If the remote properties file cannot be loaded (network issues, repository unavailable):

```
Warning: Could not fetch modules-untouched apache.properties: Connection timeout
Apache 2.4.66 not found in modules-untouched repository
```

The system will automatically fall back to checking for direct archive files in the repository.

### Version Not Found

If a version is not found in any location:

```
Failed to obtain Apache binaries for version 2.4.66

You can manually:
1. Download and extract Apache binaries to: bin/apache2.4.66/Apache24/
2. Add version 2.4.66 to https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties
3. Upload Apache binaries to: https://github.com/Bearsampp/modules-untouched/tree/main/apache2.4.66/
```

## Configuration

No additional configuration is required. The feature works automatically when building any Apache version.

### Timeout Settings

The remote properties loader uses these timeout settings:

| Setting                          | Value                                                                        |
|----------------------------------|------------------------------------------------------------------------------|
| Connect Timeout                  | 10 seconds                                                                   |
| Read Timeout                     | 10 seconds                                                                   |

These can be adjusted in the `fetchModulesUntouchedProperties()` function if needed.

## Testing

To test the remote properties support:

| Test                             | Steps                                                                        | Expected Result                              |
|----------------------------------|------------------------------------------------------------------------------|----------------------------------------------|
| Test with existing version       | `gradle release -PbundleVersion=2.4.62`                                      | Should fetch from remote apache.properties   |
| Test with new version            | Build a version recently added to remote                                     | Should fetch from remote apache.properties   |
| Test network error handling      | Disconnect network, build version                                            | Should show appropriate error message        |
| Test fallback mechanism          | Build version not in apache.properties                                       | Should try direct archive detection          |

## Maintenance

### Adding New Versions

To add a new Apache version, use one of these methods:

| Option | Method                                    | Steps                                                        | Scope        |
|--------|-------------------------------------------|--------------------------------------------------------------|--------------|
| 1      | Add to remote apache.properties (Recommended) | Edit `modules/apache.properties`, add version, commit    | All builds   |
| 2      | Upload to modules-untouched repository    | Create `apache2.4.XX/` directory, upload binaries            | All builds   |

**Option 1 Details (Recommended):**
1. Edit `modules/apache.properties` in modules-untouched repository
2. Add line: `2.4.XX = https://download-url-here`
3. Commit and push
4. Version is now immediately available to all builds

**Option 2 Details:**
1. Create directory: `apache2.4.XX/` in modules-untouched repository
2. Upload Apache binaries (as .7z or .zip archive)
3. Version can be auto-discovered by the build system

**Note:** The local `releases.properties` file has been removed. All version management is now centralized in the modules-untouched repository.

## Related Files

| File/Resource                    | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| `build.gradle`                   | Contains the implementation                                                  |
| Remote apache.properties         | https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties |
| modules-untouched repository     | https://github.com/Bearsampp/modules-untouched                               |

**Note:** The `releases.properties` file has been removed as of the latest update.

## See Also

| Document                         | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| [GRADLE_UPDATES.md](GRADLE_UPDATES.md) | General Gradle updates                                                 |
| [CHANGES_SUMMARY.md](CHANGES_SUMMARY.md) | Summary of all changes                                               |
| [modules-untouched repository](https://github.com/Bearsampp/modules-untouched) | Source repository                    |
