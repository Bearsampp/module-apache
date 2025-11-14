# Remote apache.properties Support

## Overview

The Gradle build system now supports fetching Apache version information from the remote `apache.properties` file hosted in the modules-untouched repository. This allows building Apache versions that are not yet listed in the local `releases.properties` file.

## Remote Properties URL

```
https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/apache.properties
```

## Version Resolution Priority

When building an Apache version, the system follows this priority order:

| Priority | Source                                    | Description                                                  |
|----------|-------------------------------------------|--------------------------------------------------------------|
| 1        | Local `releases.properties`               | Check the local file first                                   |
| 2        | Remote `apache.properties`                | Fetch from modules-untouched repository                      |
| 3        | Direct repository download                | Download from `apache{version}/` directory                   |

## How It Works

### Function: loadRemoteApacheProperties()

A new helper function has been added to `build.gradle`:

```groovy
def loadRemoteApacheProperties() {
    def remoteUrl = "https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/apache.properties"
    
    try {
        println "Loading remote apache.properties from modules-untouched..."
        def connection = new URL(remoteUrl).openConnection()
        connection.setConnectTimeout(10000)
        connection.setReadTimeout(10000)
        
        def remoteProps = new Properties()
        connection.inputStream.withStream { stream ->
            remoteProps.load(stream)
        }
        
        println "  Loaded ${remoteProps.size()} versions from remote apache.properties"
        return remoteProps
    } catch (Exception e) {
        println "  Warning: Could not load remote apache.properties: ${e.message}"
        return new Properties()
    }
}
```

### Integration with downloadAndExtractApache()

The `downloadAndExtractApache()` function has been updated to use the remote properties:

```groovy
def downloadUrl = releases.getProperty(version)
if (!downloadUrl) {
    // Check remote apache.properties from modules-untouched
    println "Version ${version} not found in releases.properties"
    println "Checking remote apache.properties from modules-untouched..."
    
    def remoteProps = loadRemoteApacheProperties()
    downloadUrl = remoteProps.getProperty(version)
    
    if (downloadUrl) {
        println "  Found version ${version} in remote apache.properties"
        println "  URL: ${downloadUrl}"
    } else {
        // Fallback to direct repository download
        // ...
    }
}
```

## Benefits

| Benefit                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Automatic Version Discovery      | No need to manually update `releases.properties` for every new version       |
| Centralized Version Management   | All versions maintained in one place (modules-untouched)                     |
| Fallback Support                 | Multiple fallback mechanisms ensure builds can proceed                       |
| Network Resilience               | Graceful handling of network errors with clear error messages                |

## Usage Example

### Building a Version Not in releases.properties

```bash
# If version 2.4.66 is in remote apache.properties but not in local releases.properties
gradle release -PbundleVersion=2.4.66
```

**Output:**
```
Version 2.4.66 not found in releases.properties
Checking remote apache.properties from modules-untouched...
Loading remote apache.properties from modules-untouched...
  Loaded 15 versions from remote apache.properties
  Found version 2.4.66 in remote apache.properties
  URL: https://github.com/Bearsampp/modules-untouched/releases/download/...
Downloading Apache 2.4.66 from:
  https://github.com/Bearsampp/modules-untouched/releases/download/...
```

## Error Handling

### Network Errors

If the remote properties file cannot be loaded (network issues, repository unavailable):

```
Warning: Could not load remote apache.properties: Connection timeout
Version 2.4.66 not found in remote apache.properties
Checking modules-untouched repository on GitHub...
```

The system will automatically fall back to checking the direct repository download.

### Version Not Found

If a version is not found in any location:

```
Version 2.4.66 not found in releases.properties, remote apache.properties, or modules-untouched repository.

Please either:
1. Add the version to releases.properties with a download URL
2. Add the version to https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties
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

These can be adjusted in the `loadRemoteApacheProperties()` function if needed.

## Testing

To test the remote properties support:

| Test                             | Steps                                                                        | Expected Result                              |
|----------------------------------|------------------------------------------------------------------------------|----------------------------------------------|
| Test with existing version       | `gradle release -PbundleVersion=2.4.62`                                      | Should use local releases.properties         |
| Test with version only in remote | Remove version from local, build that version                                | Should fetch from remote apache.properties   |
| Test network error handling      | Disconnect network, build version not in local                               | Should show appropriate error message        |

## Maintenance

### Adding New Versions

To add a new Apache version:

| Option | Method                                    | Steps                                                        | Scope        |
|--------|-------------------------------------------|--------------------------------------------------------------|--------------|
| 1      | Add to remote apache.properties (Recommended) | Edit `modules/apache.properties`, add version, commit    | All builds   |
| 2      | Add to local releases.properties          | Edit local file, add version line                            | Local only   |
| 3      | Upload to modules-untouched repository    | Create `apache2.4.XX/` directory, upload binaries            | All builds   |

**Option 1 Details (Recommended):**
1. Edit `modules/apache.properties` in modules-untouched repository
2. Add line: `2.4.XX = https://download-url-here`
3. Commit and push
4. Version is now available to all builds

**Option 2 Details:**
1. Edit local `releases.properties`
2. Add line: `2.4.XX = https://download-url-here`
3. Version is available for local builds only

**Option 3 Details:**
1. Create directory: `apache2.4.XX/`
2. Upload Apache binaries
3. Version can be auto-discovered

## Related Files

| File/Resource                    | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| `build.gradle`                   | Contains the implementation                                                  |
| `releases.properties`            | Local version definitions                                                    |
| Remote apache.properties         | https://github.com/Bearsampp/modules-untouched/blob/main/modules/apache.properties |

## See Also

| Document                         | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| [GRADLE_UPDATES.md](GRADLE_UPDATES.md) | General Gradle updates                                                 |
| [CHANGES_SUMMARY.md](CHANGES_SUMMARY.md) | Summary of all changes                                               |
| [modules-untouched repository](https://github.com/Bearsampp/modules-untouched) | Source repository                    |
