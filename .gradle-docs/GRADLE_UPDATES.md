# Gradle Build Updates - Module Apache

## Overview
Updated the Gradle build configuration to match the behavior and functions from the `module-bruno` reference repository (gradle-convert branch).

## Changes Made

### 1. Build Path Configuration

**Added configurable build base path** with priority system:

| Priority | Source                                    | Example                                      |
|----------|-------------------------------------------|----------------------------------------------|
| 1        | `build.path` in `build.properties`        | `build.path = C:/Bearsampp-build`            |
| 2        | `BEARSAMPP_BUILD_PATH` environment var    | `set BEARSAMPP_BUILD_PATH=C:/Bearsampp-build`|
| 3        | Default                                   | `${rootDir}/bearsampp-build`                 |

**Updated output structure** to: `{buildBasePath}/{bundleType}/{bundleName}/{bundleRelease}`

- Example: `bearsampp-build/bins/apache/2025.8.15/`

### 2. 7-Zip Executable Detection

**Improvements:**

| Feature                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Function rename                  | `findSevenZip()` → `find7ZipExecutable()` for consistency                    |
| 7Z_HOME support                  | Environment variable for custom 7-Zip locations                              |
| Expanded search paths            | Includes D: drive installations                                              |
| Improved error messages          | Mentions 7Z_HOME environment variable                                        |

### 3. Version Management

**Simplified version listing** with `getAvailableVersions()` function:

| Feature                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Return type                      | Flat list of version strings                                                 |
| Directory scanning               | Automatically checks `bin/` and `bin/archived/`                              |
| Deduplication                    | Removes duplicates and sorts versions                                        |
| Location tags                    | Shows `[bin]` or `[bin/archived]` for each version                           |
| Formatting                       | Improved with proper padding                                                 |
| Error messages                   | Better messages for invalid selections                                       |

### 4. Task Improvements

| Task            | Improvements                                                                 |
|-----------------|------------------------------------------------------------------------------|
| `info`          | Added `Build Path` display showing resolved build base path                  |
| `listVersions`  | Simplified output, shows location tags, cleaner formatting                   |
| `verify`        | Added 7-Zip check when `bundle.format = 7z`                                  |
| `release`       | Updated build path structure, improved version selection                     |
| `releaseAll`    | Supports `bin/archived/` directory, uses simplified version detection        |

### 5. Settings File

**Created `settings.gradle`** with:

| Feature                          | Description                                                                  |
|----------------------------------|------------------------------------------------------------------------------|
| Project name                     | Configured as 'module-apache'                                                |
| Configuration cache              | Enabled stable configuration cache feature                                   |
| Build cache                      | Configured local build cache                                                 |
| Initialization message           | Displays startup message                                                     |

### 6. Code Consistency
- Aligned function names with Bruno module conventions
- Standardized error messages and user prompts
- Improved code comments and documentation
- Consistent formatting throughout

## New Features

### Configurable Build Path

You can now configure the build output path in three ways:

| Priority | Method                          | Example                                                      |
|----------|---------------------------------|--------------------------------------------------------------|
| 1        | In `build.properties`           | `build.path = C:/Bearsampp-build`                            |
| 2        | Environment variable            | `set BEARSAMPP_BUILD_PATH=C:/Bearsampp-build`                |
| 3        | Default                         | Uses `../bearsampp-build` relative to project root           |

### Archived Versions Support
The build system now automatically detects and supports versions in both:
- `bin/` - Current/active versions
- `bin/archived/` - Archived/older versions

Both locations are checked when building or listing versions.

### Enhanced 7-Zip Detection
Set the `7Z_HOME` environment variable to specify a custom 7-Zip installation:
```bash
set 7Z_HOME=D:\Tools\7-Zip
```

## Usage Examples

| Task                                      | Command                                      | Description                                  |
|-------------------------------------------|----------------------------------------------|----------------------------------------------|
| List all available versions               | `gradle listVersions`                        | Shows versions in bin/ and bin/archived/     |
| Build a specific version                  | `gradle release -PbundleVersion=2.4.62`      | Builds specified Apache version              |
| Interactive build                         | `gradle release`                             | Prompts for version selection                |
| Build all versions                        | `gradle releaseAll`                          | Builds all available versions                |
| Verify build environment                  | `gradle verify`                              | Checks prerequisites and configuration       |
| Display build information                 | `gradle info`                                | Shows build configuration details            |
| Clean build artifacts                     | `gradle clean`                               | Removes build artifacts                      |

## Output Structure

Archives are now created in:
```
{buildBasePath}/{bundleType}/{bundleName}/{bundleRelease}/
```

Example:
```
bearsampp-build/
  bins/
    apache/
      2025.8.15/
        bearsampp-apache-2.4.62-2025.8.15.7z
        bearsampp-apache-2.4.62-2025.8.15.7z.md5
        bearsampp-apache-2.4.62-2025.8.15.7z.sha1
        bearsampp-apache-2.4.62-2025.8.15.7z.sha256
        bearsampp-apache-2.4.62-2025.8.15.7z.sha512
```

## Compatibility

| Feature                          | Status                                                                      |
|----------------------------------|-----------------------------------------------------------------------------|
| Existing `build.properties`      | ✅ Fully compatible                                                         |
| Bin directory structure          | ✅ Backward compatible                                                      |
| Archived versions                | ✅ Supports both current and archived directories                           |
| Archive formats                  | ✅ Supports both 7z and zip formats                                         |

## Testing

All tasks have been tested and verified:

| Test                             | Status | Description                                                  |
|----------------------------------|--------|--------------------------------------------------------------|
| `gradle info`                    | ✅     | Displays build information correctly                         |
| `gradle listVersions`            | ✅     | Lists all versions from bin/ and bin/archived/               |
| `gradle verify`                  | ✅     | Checks environment including 7-Zip                           |
| Build path resolution            | ✅     | Works with all three priority levels                         |
| Interactive version selection    | ✅     | Displays location tags correctly                             |
| Archive creation                 | ✅     | Follows new path structure                                   |

## Migration Notes

If you have an existing `build.path` property in `build.properties`, it will continue to work as before. The new priority system ensures backward compatibility while adding flexibility through environment variables.

## Reference

Based on: https://github.com/Bearsampp/module-bruno/tree/gradle-convert
