# Gradle Build - Ant-Compatible Temporary Paths

## Overview

The Gradle build configuration for module-apache has been updated to use the same temporary directory structure as Ant builds, ensuring consistency across the Bearsampp build system.

## Path Structure

### Ant Build Paths (from module-composer)

When running `ant release` in module-composer, files are placed in:

```
bearsampp-build/
└── tmp/
    ├── bundles_build/{type}/{module-name}/    # Build staging area
    ├── bundles_prep/{type}/{module-name}/     # Preparation area
    └── bundles_src/                           # Source downloads
```

Where `{type}` is the bundle type (tools, apps, bins, etc.)

### Gradle Build Paths (module-apache)

The Gradle build now uses the **same structure with type organization**:

```groovy
buildBasePath = file("${rootDir}/bearsampp-build").absolutePath
buildTmpPath = file("${buildBasePath}/tmp").absolutePath
bundleTmpBuildPath = file("${buildTmpPath}/bundles_build/${bundleType}/${bundleName}").absolutePath
bundleTmpPrepPath = file("${buildTmpPath}/bundles_prep/${bundleType}/${bundleName}").absolutePath
bundleTmpSrcPath = file("${buildTmpPath}/bundles_src").absolutePath
```

## Path Definitions

| Variable | Path | Purpose |
|----------|------|---------|
| `buildBasePath` | `E:/Bearsampp-development/bearsampp-build` | Root build directory |
| `buildTmpPath` | `E:/Bearsampp-development/bearsampp-build/tmp` | Temporary build files |
| `bundleTmpBuildPath` | `E:/Bearsampp-development/bearsampp-build/tmp/bundles_build/bins/apache` | Build staging for apache |
| `bundleTmpPrepPath` | `E:/Bearsampp-development/bearsampp-build/tmp/bundles_prep/bins/apache` | Preparation area for apache |
| `bundleTmpSrcPath` | `E:/Bearsampp-development/bearsampp-build/tmp/bundles_src` | Downloaded source files |
| `bundleTmpDownloadPath` | `E:/Bearsampp-development/bearsampp-build/tmp/downloads/apache` | Downloaded archives |
| `bundleTmpExtractPath` | `E:/Bearsampp-development/bearsampp-build/tmp/extract/apache` | Extracted archives |

## How It Works

### During Release Build

1. **Preparation Phase** (`bundleTmpPrepPath`)
   - Files are copied to `bearsampp-build/tmp/bundles_prep/bins/apache/{version}/`
   - Configuration files are merged
   - Modules are processed and injected
   - Ready for compression

2. **Build Phase** (`bundleTmpBuildPath`)
   - Reserved for future use (currently using prep path)
   - Can be used for intermediate build steps

3. **Source Downloads** (`bundleTmpSrcPath`)
   - Downloaded binaries stored in `bearsampp-build/tmp/bundles_src/`
   - Shared across all modules

4. **Download/Extract Cache** (`bundleTmpDownloadPath`, `bundleTmpExtractPath`)
   - Downloaded archives cached in `bearsampp-build/tmp/downloads/apache/`
   - Extracted files cached in `bearsampp-build/tmp/extract/apache/`
   - Reused across builds to avoid re-downloading

5. **Final Output**
   - Archives created in `bearsampp-build/bins/apache/{release}/`
   - Hash files generated alongside archives

## Example: Building Apache 2.4.62

```bash
gradle release -PbundleVersion=2.4.62
```

**File Flow:**

1. Source: `module-apache/bin/apache2.4.62/Apache24/` or download from releases.properties
2. Prep: `bearsampp-build/tmp/bundles_prep/bins/apache/apache2.4.62/`
3. Archive: `bearsampp-build/bins/apache/2025.10.14/bearsampp-apache-2.4.62-2025.10.14.7z`
4. Hashes: `.md5`, `.sha1`, `.sha256`, `.sha512` files

## Verification

To verify the paths are correctly configured:

```bash
gradle info
```

This will display all configured paths including the new tmp directories.

## Benefits

1. **Consistency**: Same directory structure as Ant builds
2. **Compatibility**: Works seamlessly with existing Bearsampp build infrastructure
3. **Clarity**: Clear separation of build stages (prep, build, src, downloads, extract)
4. **Maintainability**: Easy to understand and debug build process
5. **Efficiency**: Caching of downloads and extracts reduces build time

## Comparison with Previous Gradle Setup

### Before (Local Build Directory)

```
module-apache/
└── build/
    ├── tmp/           # Local to module
    └── prep/          # Local to module
```

### After (Shared Build Directory with Type Organization)

```
bearsampp-build/
└── tmp/
    ├── bundles_build/bins/apache/    # Shared location with type
    ├── bundles_prep/bins/apache/     # Shared location with type
    ├── bundles_src/                  # Shared location
    ├── downloads/apache/             # Download cache
    └── extract/apache/               # Extract cache
```

## Migration Notes

- The change is **backward compatible** - existing builds will continue to work
- The `bearsampp-build/tmp` directory is automatically created if it doesn't exist
- Local `build/` directory is still used for Gradle's internal files (downloads, extract cache for fallback)
- Final archives are placed in `bearsampp-build/bins/apache/{release}/` (unchanged)

## Configuration Options

You can customize the build base path in three ways (priority order):

1. **build.properties**: Add `build.path=/custom/path`
2. **Environment variable**: Set `BEARSAMPP_BUILD_PATH=/custom/path`
3. **Default**: Uses `{rootDir}/bearsampp-build`

Example in `build.properties`:

```properties
# Custom build path (optional)
build.path=E:/custom-build-location
```

## Related Files

- `build.gradle` - Main build configuration with path definitions
- `build.properties` - Bundle configuration (name, release, type, format)
- `releases.properties` - Download URLs for Apache versions

## See Also

- Ant build configuration: `E:/Bearsampp-development/dev/build/build-commons.xml`
- Ant bundle configuration: `E:/Bearsampp-development/dev/build/build-bundle.xml`
- Module composer example: `E:/Bearsampp-development/module-composer/build.xml`
- Module bruno example: `E:/Bearsampp-development/module-bruno/build.gradle`
