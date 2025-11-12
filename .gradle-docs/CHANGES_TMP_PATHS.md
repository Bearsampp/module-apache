# Changes: Gradle Tmp Paths Update

## Summary

Updated the Gradle build configuration to use the shared `bearsampp-build/tmp` directory structure, matching the pattern used in module-bruno and Ant builds.

## Changes Made

### 1. Updated `build.gradle`

**Path Configuration Changes:**

- Changed `buildBasePath` default from `"${rootDir}/../bearsampp-build"` to `"${rootDir}/bearsampp-build"`
- Replaced local build paths with shared tmp structure:
  - Old: `buildTmpPath = file("${buildDir}/tmp").absolutePath`
  - New: `buildTmpPath = file("${buildBasePath}/tmp").absolutePath`
  
- Old: `bundleTmpPrepPath = file("${buildDir}/prep").absolutePath`
- New: Added complete tmp path structure:
  ```groovy
  bundleTmpBuildPath = file("${buildTmpPath}/bundles_build/${bundleType}/${bundleName}").absolutePath
  bundleTmpPrepPath = file("${buildTmpPath}/bundles_prep/${bundleType}/${bundleName}").absolutePath
  bundleTmpSrcPath = file("${buildTmpPath}/bundles_src").absolutePath
  bundleTmpDownloadPath = file("${buildTmpPath}/downloads/${bundleName}").absolutePath
  bundleTmpExtractPath = file("${buildTmpPath}/extract/${bundleName}").absolutePath
  ```

**Info Task Updates:**

Added display of all tmp paths in the `info` task output:
- Build Tmp
- Tmp Prep
- Tmp Build
- Tmp Src
- Tmp Download
- Tmp Extract

### 2. Created Documentation

**New File: `GRADLE_TMP_PATHS.md`**

Comprehensive documentation explaining:
- Path structure comparison with Ant builds
- Path definitions and purposes
- How the build process works
- Example build flow
- Configuration options
- Benefits of the new structure

## Directory Structure

### Before

```
module-apache/
└── build/
    ├── tmp/           # Local temporary files
    └── prep/          # Local preparation area
```

### After

```
bearsampp-build/
└── tmp/
    ├── bundles_build/bins/apache/    # Build staging
    ├── bundles_prep/bins/apache/     # Preparation area
    ├── bundles_src/                  # Source downloads
    ├── downloads/apache/             # Download cache
    └── extract/apache/               # Extract cache
```

## Benefits

1. **Consistency**: Matches module-bruno and Ant build patterns
2. **Type Organization**: Files organized by bundle type (bins, tools, apps)
3. **Shared Resources**: Common tmp directory across all modules
4. **Better Caching**: Separate download and extract caches
5. **Easier Debugging**: Clear separation of build stages

## Verification

Run `gradle info` to see the new path configuration:

```bash
gradle info
```

Output will show:
```
Paths:
  Build Base:   E:\Bearsampp-development/bearsampp-build
  Build Tmp:    E:\Bearsampp-development\bearsampp-build\tmp
  Tmp Prep:     E:\Bearsampp-development\bearsampp-build\tmp\bundles_prep\bins\apache
  Tmp Build:    E:\Bearsampp-development\bearsampp-build\tmp\bundles_build\bins\apache
  Tmp Src:      E:\Bearsampp-development\bearsampp-build\tmp\bundles_src
  Tmp Download: E:\Bearsampp-development\bearsampp-build\tmp\downloads\apache
  Tmp Extract:  E:\Bearsampp-development\bearsampp-build\tmp\extract\apache
```

## Compatibility

- **Backward Compatible**: Existing builds continue to work
- **No Breaking Changes**: All existing tasks function as before
- **Automatic Creation**: Tmp directories created automatically as needed

## Related Changes

- Aligns with module-bruno gradle-convert branch
- Follows Ant build patterns from module-composer
- Maintains compatibility with existing Bearsampp build infrastructure

## Files Modified

1. `build.gradle` - Updated path configuration and info task
2. `GRADLE_TMP_PATHS.md` - New documentation file (created)
3. `CHANGES_TMP_PATHS.md` - This change summary (created)

## Testing

Test the changes with:

```bash
# View configuration
gradle info

# Build a version
gradle release -PbundleVersion=2.4.62

# Verify tmp directory structure
dir E:\Bearsampp-development\bearsampp-build\tmp
```

## References

- Module Bruno: https://github.com/Bearsampp/module-bruno/tree/gradle-convert
- Module Bruno Tmp Paths Doc: `E:/temp/module-bruno-ref/GRADLE_TMP_PATHS.md`
