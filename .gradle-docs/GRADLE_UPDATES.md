# Gradle Build Updates - Module Apache

## Overview
Updated the Gradle build configuration to match the behavior and functions from the `module-bruno` reference repository (gradle-convert branch).

## Changes Made

### 1. Build Path Configuration
- **Added configurable build base path** with priority system:
  1. `build.path` property in `build.properties`
  2. `BEARSAMPP_BUILD_PATH` environment variable
  3. Default: `${rootDir}/../bearsampp-build`
- **Updated output structure** to: `{buildBasePath}/{bundleType}/{bundleName}/{bundleRelease}`
  - Example: `bearsampp-build/bins/apache/2025.8.15/`

### 2. 7-Zip Executable Detection
- **Renamed function** from `findSevenZip()` to `find7ZipExecutable()` for consistency
- **Added 7Z_HOME environment variable support** for custom 7-Zip locations
- **Expanded search paths** to include D: drive installations
- **Improved error messages** to mention 7Z_HOME environment variable

### 3. Version Management
- **Simplified version listing** with `getAvailableVersions()` function
  - Returns flat list of version strings
  - Automatically checks both `bin/` and `bin/archived/` directories
  - Removes duplicates and sorts versions
- **Enhanced interactive version selection**
  - Shows location tags `[bin]` or `[bin/archived]` for each version
  - Improved formatting with proper padding
  - Better error messages for invalid selections

### 4. Task Improvements

#### `info` Task
- Added `Build Path` to the displayed information
- Shows the resolved build base path being used

#### `listVersions` Task
- Simplified output format
- Shows location tag for each version
- Cleaner display with consistent formatting

#### `verify` Task
- Added 7-Zip check when `bundle.format = 7z`
- Uses the new `find7ZipExecutable()` function

#### `release` Task
- Updated to use new build path structure
- Improved version selection display
- Better error messages with available versions list

#### `releaseAll` Task
- Updated to support `bin/archived/` directory
- Uses simplified `getAvailableVersions()` function

### 5. Settings File
- **Created `settings.gradle`** with:
  - Project name configuration
  - Stable configuration cache feature preview
  - Local build cache configuration
  - Initialization message display

### 6. Code Consistency
- Aligned function names with Bruno module conventions
- Standardized error messages and user prompts
- Improved code comments and documentation
- Consistent formatting throughout

## New Features

### Configurable Build Path
You can now configure the build output path in three ways:

1. **In build.properties:**
   ```properties
   build.path = C:/Bearsampp-build
   ```

2. **Environment variable:**
   ```bash
   set BEARSAMPP_BUILD_PATH=C:/Bearsampp-build
   ```

3. **Default:** Uses `../bearsampp-build` relative to project root

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

### List all available versions
```bash
gradle listVersions
```

### Build a specific version
```bash
gradle release -PbundleVersion=2.4.62
```

### Interactive build (prompts for version)
```bash
gradle release
```

### Build all versions
```bash
gradle releaseAll
```

### Verify build environment
```bash
gradle verify
```

### Display build information
```bash
gradle info
```

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

- Fully compatible with existing `build.properties` configuration
- Backward compatible with existing bin directory structure
- Works with both current and archived version directories
- Supports both 7z and zip archive formats

## Testing

All tasks have been tested and verified:
- ✅ `gradle info` - Displays build information correctly
- ✅ `gradle listVersions` - Lists all versions from bin/ and bin/archived/
- ✅ `gradle verify` - Checks environment including 7-Zip
- ✅ Build path resolution works with all three priority levels
- ✅ Interactive version selection displays location tags
- ✅ Archive creation follows new path structure

## Migration Notes

If you have an existing `build.path` property in `build.properties`, it will continue to work as before. The new priority system ensures backward compatibility while adding flexibility through environment variables.

## Reference

Based on: https://github.com/Bearsampp/module-bruno/tree/gradle-convert
