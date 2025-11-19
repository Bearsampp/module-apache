# Bearsampp Module Apache - Gradle Build Documentation

## Table of Contents

- [Overview](#overview)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Build Tasks](#build-tasks)
- [Configuration](#configuration)
- [Architecture](#architecture)
- [Troubleshooting](#troubleshooting)
- [Migration Guide](#migration-guide)

---

## Overview

The Bearsampp Module Apache project has been converted to a **pure Gradle build system**, replacing the legacy Ant build configuration. This provides:

- **Modern Build System**     - Native Gradle tasks and conventions
- **Better Performance**       - Incremental builds and caching
- **Simplified Maintenance**   - Pure Groovy/Gradle DSL
- **Enhanced Tooling**         - IDE integration and dependency management
- **Cross-Platform Support**   - Works on Windows, Linux, and macOS

### Project Information

| Property          | Value                                    |
|-------------------|------------------------------------------|
| **Project Name**  | module-apache                            |
| **Group**         | com.bearsampp.modules                    |
| **Type**          | Apache HTTP Server Module Builder        |
| **Build Tool**    | Gradle 6.0+                              |
| **Language**      | Groovy (Gradle DSL)                      |

---

## Quick Start

### Prerequisites

| Requirement       | Version       | Purpose                                  |
|-------------------|---------------|------------------------------------------|
| **Java**          | 8+            | Required for Gradle execution            |
| **Gradle**        | 6.0+          | Build automation tool                    |
| **7-Zip**         | Latest        | Archive extraction and creation          |

### Basic Commands

```bash
# Display build information
gradle info

# List all available tasks
gradle tasks

# Verify build environment
gradle verify

# Build a release (interactive)
gradle release

# Build a specific version (non-interactive)
gradle release -PbundleVersion=2.4.62

# Build all versions
gradle releaseAll

# Clean build artifacts
gradle clean
```

---

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/bearsampp/module-apache.git
cd module-apache
```

### 2. Verify Environment

```bash
gradle verify
```

This will check:
- Java version (8+)
- Required files (build.properties)
- Directory structure (bin/)
- Build dependencies

### 3. List Available Versions

```bash
gradle listVersions
```

### 4. Build Your First Release

```bash
# Interactive mode (prompts for version)
gradle release

# Or specify version directly
gradle release -PbundleVersion=2.4.62
```

---

## Build Tasks

### Core Build Tasks

| Task                  | Description                                      | Example                                  |
|-----------------------|--------------------------------------------------|------------------------------------------|
| `release`             | Build and package release (interactive/non-interactive) | `gradle release -PbundleVersion=2.4.62` |
| `releaseAll`          | Build all available versions                     | `gradle releaseAll`                      |
| `clean`               | Clean build artifacts and temporary files        | `gradle clean`                           |

### Verification Tasks

| Task                      | Description                                  | Example                                      |
|---------------------------|----------------------------------------------|----------------------------------------------|
| `verify`                  | Verify build environment and dependencies    | `gradle verify`                              |
| `validateProperties`      | Validate build.properties configuration      | `gradle validateProperties`                  |
| `checkModules`            | Check Apache modules configuration           | `gradle checkModules`                        |
| `checkModulesUntouched`   | Check modules-untouched integration          | `gradle checkModulesUntouched`               |

### Information Tasks

| Task                | Description                                      | Example                    |
|---------------------|--------------------------------------------------|----------------------------|
| `info`              | Display build configuration information          | `gradle info`              |
| `listVersions`      | List available bundle versions in bin/           | `gradle listVersions`      |
| `listReleases`      | List all available releases from modules-untouched | `gradle listReleases`    |

### Task Groups

| Group            | Purpose                                          |
|------------------|--------------------------------------------------|
| **build**        | Build and package tasks                          |
| **verification** | Verification and validation tasks                |
| **help**         | Help and information tasks                       |

---

## Configuration

### build.properties

The main configuration file for the build:

```properties
bundle.name     = apache
bundle.release  = 2025.8.15
bundle.type     = bins
bundle.format   = 7z
```

| Property          | Description                          | Example Value  |
|-------------------|--------------------------------------|----------------|
| `bundle.name`     | Name of the bundle                   | `apache`       |
| `bundle.release`  | Release version                      | `2025.8.15`    |
| `bundle.type`     | Type of bundle                       | `bins`         |
| `bundle.format`   | Archive format (7z or zip)           | `7z`           |

### Build Path Configuration

You can configure the build output path in three ways (priority order):

1. **In build.properties:**
   ```properties
   build.path = C:/Bearsampp-build
   ```

2. **Environment variable:**
   ```bash
   set BEARSAMPP_BUILD_PATH=C:/Bearsampp-build
   ```

3. **Default:** Uses `../bearsampp-build` relative to project root

### gradle.properties

Gradle-specific configuration:

```properties
# Gradle daemon configuration
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true

# JVM settings
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m
```

### Directory Structure

```
module-apache/
├── .gradle-docs/          # Gradle documentation
│   ├── README.md          # Main documentation (this file)
│   ├── GRADLE_UPDATES.md  # Detailed updates documentation
│   ├── CHANGES_SUMMARY.md # Summary of changes
│   └── ...
├── bin/                   # Apache version bundles
│   ├── apache2.4.62/
│   ├── apache2.4.63/
│   ├── archived/          # Archived versions
│   │   └── apache2.4.61/
│   └── ...
├── build.gradle           # Main Gradle build script
├── settings.gradle        # Gradle settings
├── build.properties       # Build configuration
└── releases.properties    # Available Apache releases

bearsampp-build/           # External build directory (outside repo)
├── tmp/                   # Temporary build files
│   ├── bundles_prep/bins/apache/      # Prepared bundles
│   ├── bundles_build/bins/apache/     # Build staging
│   ├── bundles_src/                   # Source bundles
│   ├── downloads/apache/              # Downloaded dependencies
│   └── extract/apache/                # Extracted archives
└── bins/apache/           # Final packaged archives
    └── 2025.8.15/         # Release version
        ├── bearsampp-apache-2.4.62-2025.8.15.7z
        ├── bearsampp-apache-2.4.62-2025.8.15.7z.md5
        └── ...
```

---

## Architecture

### Build Process Flow

```
1. User runs: gradle release -PbundleVersion=2.4.62
                    ↓
2. Validate environment and version
                    ↓
3. Resolve Apache binaries:
   - Check bin/apache2.4.62/
   - Check bin/archived/apache2.4.62/
   - Check modules-untouched repository
                    ↓
4. Create preparation directory (tmp/bundles_prep/)
                    ↓
5. Copy base Apache files
   - Exclude: cgi-bin, conf/original, conf/ssl, error, htdocs,
              icons, include, lib, logs/*, tools
                    ↓
6. Copy bundle customizations
   - Configuration files
   - Custom modules
   - Documentation
                    ↓
7. Process modules (if modules.properties exists)
   - Download modules
   - Inject into httpd.conf
                    ↓
8. Copy to build staging (tmp/bundles_build/)
                    ↓
9. Package into archive in bearsampp-build/bins/apache/{bundle.release}/
   - The archive includes the top-level folder: apache{version}/
```

### Packaging Details

- **Archive name format**: `bearsampp-apache-{version}-{bundle.release}.{7z|zip}`
- **Location**: `bearsampp-build/bins/apache/{bundle.release}/`
  - Example: `bearsampp-build/bins/apache/2025.8.15/bearsampp-apache-2.4.62-2025.8.15.7z`
- **Content root**: The top-level folder inside the archive is `apache{version}/` (e.g., `apache2.4.62/`)
- **Structure**: The archive contains the Apache version folder at the root with all Apache files inside

**Archive Structure Example**:
```
bearsampp-apache-2.4.62-2025.8.15.7z
└── apache2.4.62/              ← Version folder at root
    ├── bin/
    │   ├── httpd.exe
    │   └── ...
    ├── conf/
    │   ├── httpd.conf
    │   └── ...
    ├── modules/
    │   ├── mod_rewrite.so
    │   └── ...
    └── ...
```

**Verification Commands**:

```bash
# List archive contents with 7z
7z l bearsampp-build/bins/apache/2025.8.15/bearsampp-apache-2.4.62-2025.8.15.7z | more

# You should see entries beginning with:
#   apache2.4.62/bin/httpd.exe
#   apache2.4.62/conf/httpd.conf
#   apache2.4.62/modules/mod_rewrite.so
#   apache2.4.62/...

# Extract and inspect with PowerShell (zip example)
Expand-Archive -Path bearsampp-build/bins/apache/2025.8.15/bearsampp-apache-2.4.62-2025.8.15.zip -DestinationPath .\_inspect
Get-ChildItem .\_inspect\apache2.4.62 | Select-Object Name

# Expected output:
#   bin/
#   conf/
#   modules/
#   ...
```

**Note**: This archive structure matches the PHP and MySQL module patterns where archives contain `{module}{version}/` at the root. This ensures consistency across all Bearsampp modules.

**Hash Files**: Each archive is accompanied by hash sidecar files:
- `.md5` - MD5 checksum
- `.sha1` - SHA-1 checksum
- `.sha256` - SHA-256 checksum
- `.sha512` - SHA-512 checksum

Example:
```
bearsampp-build/bins/apache/2025.8.15/
├── bearsampp-apache-2.4.62-2025.8.15.7z
├── bearsampp-apache-2.4.62-2025.8.15.7z.md5
├── bearsampp-apache-2.4.62-2025.8.15.7z.sha1
├── bearsampp-apache-2.4.62-2025.8.15.7z.sha256
└── bearsampp-apache-2.4.62-2025.8.15.7z.sha512
```

### Version Resolution Strategy

The build system resolves Apache versions using a multi-tier fallback strategy:

```
┌─────────────────────────────────────────────────────────────┐
│ User requests version (e.g., 2.4.62)                        │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌───────────────────────────��─────────────────────────────────┐
│ 1. Check bin/ directory                                     │
│    ✓ Found: Use local binaries                              │
│    ✗ Not found: Continue to step 2                          │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│ 2. Check bin/archived/ directory                            │
│    ✓ Found: Use local binaries                              │
│    ✗ Not found: Continue to step 3                          │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────��────────────┐
│ 3. Check modules-untouched apache.properties                │
│    ✓ Found: Download from URL                               │
│    ✗ Not found: Continue to step 4                          │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│ 4. Check modules-untouched repository directly              │
│    ✓ Found: Download from apache{version}/ directory        │
│    ✗ Not found: Show error with instructions                │
└─────────────────────────────────────────────────────────────┘
```

### Modules Processing

Each Apache version can have a `modules.properties` file:

```properties
mod_fcgid=https://www.apachelounge.com/download/VS17/modules/mod_fcgid-2.3.10-win64-VS17.zip
mod_security=https://www.apachelounge.com/download/VS17/modules/mod_security2-2.9.7-win64-VS17.zip
```

The build system:
1. Downloads each module
2. Extracts the module files
3. Copies to the `modules/` directory
4. Injects `LoadModule` directives into `httpd.conf`

---

## Troubleshooting

### Common Issues

#### Issue: "Dev path not found"

**Symptom:**
```
Dev path not found: E:/Bearsampp-development/dev
```

**Solution:**
This is a warning only. The dev path is optional for most tasks. If you need it, ensure the `dev` project exists in the parent directory.

---

#### Issue: "Bundle version not found"

**Symptom:**
```
Bundle version not found: E:/Bearsampp-development/module-apache/bin/apache2.4.99
```

**Solution:**
1. List available versions: `gradle listVersions`
2. Use an existing version: `gradle release -PbundleVersion=2.4.62`
3. Check modules-untouched: `gradle listReleases`

---

#### Issue: "7-Zip not found"

**Symptom:**
```
7-Zip not found. Please install 7-Zip or set 7Z_HOME environment variable.
```

**Solution:**
1. Install 7-Zip from https://www.7-zip.org/
2. Or set `7Z_HOME` environment variable: `set 7Z_HOME=C:\Program Files\7-Zip`
3. Or change `bundle.format` to `zip` in build.properties

---

#### Issue: "Java version too old"

**Symptom:**
```
Java 8+ required
```

**Solution:**
1. Check Java version: `java -version`
2. Install Java 8 or higher
3. Update JAVA_HOME environment variable

---

### Debug Mode

Run Gradle with debug output:

```bash
gradle release -PbundleVersion=2.4.62 --info
gradle release -PbundleVersion=2.4.62 --debug
```

### Clean Build

If you encounter issues, try a clean build:

```bash
gradle clean
gradle release -PbundleVersion=2.4.62
```

---

## Migration Guide

### From Ant to Gradle

The project has been fully migrated from Ant to Gradle. Here's what changed:

#### Removed Files

| File              | Status    | Replacement                |
|-------------------|-----------|----------------------------|
| `build.xml`       | ✗ Removed | `build.gradle`             |

#### Command Mapping

| Ant Command                          | Gradle Command                              |
|--------------------------------------|---------------------------------------------|
| `ant release`                        | `gradle release`                            |
| `ant release -Dinput.bundle=2.4.62`  | `gradle release -PbundleVersion=2.4.62`     |
| `ant clean`                          | `gradle clean`                              |

#### Key Differences

| Aspect              | Ant                          | Gradle                           |
|---------------------|------------------------------|----------------------------------|
| **Build File**      | XML (build.xml)              | Groovy DSL (build.gradle)        |
| **Task Definition** | `<target name="...">`        | `tasks.register('...')`          |
| **Properties**      | `<property name="..." />`    | `ext { ... }`                    |
| **Dependencies**    | Manual downloads             | Automatic with repositories      |
| **Caching**         | None                         | Built-in incremental builds      |
| **IDE Support**     | Limited                      | Excellent (IntelliJ, Eclipse)    |

---

## Additional Resources

- [Gradle Documentation](https://docs.gradle.org/)
- [Bearsampp Project](https://github.com/bearsampp/bearsampp)
- [Apache HTTP Server](https://httpd.apache.org/)
- [Modules-Untouched Repository](https://github.com/Bearsampp/modules-untouched)

---

## Support

For issues and questions:

- **GitHub Issues**: https://github.com/bearsampp/module-apache/issues
- **Bearsampp Issues**: https://github.com/bearsampp/bearsampp/issues
- **Documentation**: https://bearsampp.com/module/apache

---

**Last Updated**: 2025-01-31  
**Version**: 2025.8.15  
**Build System**: Pure Gradle (no Ant)

Notes:
- This project deliberately does not ship the Gradle Wrapper. Install Gradle 6+ locally and run with `gradle ...`.
- Legacy Ant files have been removed and replaced with pure Gradle implementation.
