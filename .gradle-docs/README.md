# Bearsampp Module phpMyAdmin - Gradle Build Documentation

## Table of Contents

- [Overview](#overview)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Build Tasks](#build-tasks)
- [Configuration](#configuration)
- [Architecture](#architecture)
- [Troubleshooting](#troubleshooting)

---

## Overview

The Bearsampp Module phpMyAdmin project uses a **pure Gradle build system**. This provides:

- **Modern Build System**     - Native Gradle tasks and conventions
- **Better Performance**       - Incremental builds and caching
- **Simplified Maintenance**   - Pure Groovy/Gradle DSL
- **Enhanced Tooling**         - IDE integration and dependency management
- **Cross-Platform Support**   - Works on Windows, Linux, and macOS

### Project Information

| Property          | Value                                    |
|-------------------|------------------------------------------|
| **Project Name**  | module-phpmyadmin                        |
| **Group**         | com.bearsampp.modules                    |
| **Type**          | phpMyAdmin Module Builder                |
| **Build Tool**    | Gradle 8.x+                              |
| **Language**      | Groovy (Gradle DSL)                      |

---

## Quick Start

### Prerequisites

| Requirement       | Version       | Purpose                                  |
|-------------------|---------------|------------------------------------------|
| **Java**          | 8+            | Required for Gradle execution            |
| **Gradle**        | 8.0+          | Build automation tool                    |
| **7-Zip**         | Latest        | Archive creation and extraction          |

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
gradle release -PbundleVersion=5.2.1

# Build all versions
gradle releaseAll

# Clean build artifacts
gradle clean

# Clean everything including downloads
gradle cleanAll
```

---

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/bearsampp/module-phpmyadmin.git
cd module-phpmyadmin
```

### 2. Verify Environment

```bash
gradle verify
```

This will check:
- Java version (8+)
- Required files (build.properties)
- Directory structure (bin/)
- 7-Zip installation

### 3. List Available Versions

```bash
gradle listVersions
```

### 4. Build Your First Release

```bash
# Interactive mode (prompts for version)
gradle release

# Or specify version directly
gradle release -PbundleVersion=5.2.1
```

---

## Build Tasks

### Core Build Tasks

| Task                  | Description                                      | Example                                  |
|-----------------------|--------------------------------------------------|------------------------------------------|
| `release`             | Build and package release (interactive/non-interactive) | `gradle release -PbundleVersion=5.2.1` |
| `releaseAll`          | Build all available versions                     | `gradle releaseAll`                      |
| `clean`               | Clean build artifacts                            | `gradle clean`                           |
| `cleanAll`            | Clean everything including downloads             | `gradle cleanAll`                        |

### Verification Tasks

| Task                      | Description                                  | Example                                      |
|---------------------------|----------------------------------------------|----------------------------------------------|
| `verify`                  | Verify build environment and dependencies    | `gradle verify`                              |
| `validateProperties`      | Validate build.properties configuration      | `gradle validateProperties`                  |

### Information Tasks

| Task                | Description                                      | Example                    |
|---------------------|--------------------------------------------------|----------------------------|
| `info`              | Display build configuration information          | `gradle info`              |
| `listVersions`      | List available bundle versions in bin/           | `gradle listVersions`      |
| `listReleases`      | List all available releases from modules-untouched | `gradle listReleases`  |
| `checkModulesUntouched` | Check modules-untouched integration          | `gradle checkModulesUntouched` |

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
bundle.name     = phpmyadmin
bundle.release  = 2025.1.23
bundle.type     = apps
bundle.format   = 7z
```

| Property          | Description                          | Example Value  |
|-------------------|--------------------------------------|----------------|
| `bundle.name`     | Name of the bundle                   | `phpmyadmin`   |
| `bundle.release`  | Release version                      | `2025.1.23`    |
| `bundle.type`     | Type of bundle                       | `apps`         |
| `bundle.format`   | Archive format                       | `7z`           |

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
module-phpmyadmin/
├── .gradle-docs/          # Gradle documentation
│   ├── README.md          # Main documentation
│   ├── BUILD.md           # Build details
│   ├── CHANGES.md         # Change history
│   └── VERSION_FOLDER_VERIFICATION.md
├── bin/                   # phpMyAdmin version bundles
│   ├── phpmyadmin5.2.1/
│   │   ├── bearsampp.conf
│   │   └── config.inc.php
│   ├── phpmyadmin5.2.2/
│   └── archived/          # Archived versions
│       └── phpmyadmin4.9.10/
bearsampp-build/                    # External build directory (outside repo)
├── tmp/                            # Temporary build files
│   ├── bundles_prep/apps/phpmyadmin/  # Prepared bundles
│   ├── bundles_build/apps/phpmyadmin/ # Build staging
│   ├── downloads/phpmyadmin/          # Downloaded archives (cached)
│   └── extract/phpmyadmin/            # Extracted archives
└── apps/phpmyadmin/                   # Final packaged archives
    └── 2025.1.23/                     # Release version
        ├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z
        ├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.md5
        ├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.sha1
        ├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.sha256
        └── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.sha512
├── build.gradle           # Main Gradle build script
├── settings.gradle        # Gradle settings
├── build.properties       # Build configuration
└── releases.properties    # Available phpMyAdmin releases
```

---

## Architecture

### Build Process Flow

```
1. User runs: gradle release -PbundleVersion=5.2.1
                    ↓
2. Validate environment and version
                    ↓
3. Create preparation directory (tmp/prep/)
                    ↓
4. Download phpMyAdmin from:
   - modules-untouched repository (primary)
   - releases.properties (fallback)
   - Standard URL format (final fallback)
                    ↓
5. Extract phpMyAdmin archive using 7-Zip
                    ↓
6. Copy custom configuration files from bin/phpmyadmin5.2.1/
   - bearsampp.conf
   - config.inc.php
                    ↓
7. Copy to bundles_build (uncompressed for development)
                    ↓
8. Package prepared folder into archive in bearsampp-build/apps/phpmyadmin/{bundle.release}/
   - The archive includes the top-level folder: phpmyadmin{version}/
                    ↓
9. Generate hash files (MD5, SHA1, SHA256, SHA512)
```

### Packaging Details

- **Archive name format**: `bearsampp-phpmyadmin-{version}-{bundle.release}.{7z|zip}`
- **Location**: `bearsampp-build/apps/phpmyadmin/{bundle.release}/`
  - Example: `bearsampp-build/apps/phpmyadmin/2025.1.23/bearsampp-phpmyadmin-5.2.1-2025.1.23.7z`
- **Content root**: The top-level folder inside the archive is `phpmyadmin{version}/` (e.g., `phpmyadmin5.2.1/`)
- **Structure**: The archive contains the phpMyAdmin version folder at the root with all files inside

**Archive Structure Example**:
```
bearsampp-phpmyadmin-5.2.1-2025.1.23.7z
└── phpmyadmin5.2.1/        ← Version folder at root
    ├── bearsampp.conf
    ├── config.inc.php
    ├── index.php
    ├── libraries/
    ├── themes/
    └── ...
```

**Verification Commands**:

```bash
# List archive contents with 7z
7z l bearsampp-build/apps/phpmyadmin/2025.1.23/bearsampp-phpmyadmin-5.2.1-2025.1.23.7z | more

# You should see entries beginning with:
#   phpmyadmin5.2.1/index.php
#   phpmyadmin5.2.1/config.inc.php
#   phpmyadmin5.2.1/bearsampp.conf
#   phpmyadmin5.2.1/libraries/
#   phpmyadmin5.2.1/...

# Extract and inspect with PowerShell
7z x bearsampp-build/apps/phpmyadmin/2025.1.23/bearsampp-phpmyadmin-5.2.1-2025.1.23.7z -o_inspect
Get-ChildItem _inspect\phpmyadmin5.2.1 | Select-Object Name

# Expected output:
#   bearsampp.conf
#   config.inc.php
#   index.php
#   libraries/
#   themes/
#   ...
```

**Note**: This archive structure matches the module-bruno pattern where archives contain `{modulename}{version}/` at the root. This ensures consistency across all Bearsampp modules.

**Hash Files**: Each archive is accompanied by hash sidecar files:
- `.md5` - MD5 checksum
- `.sha1` - SHA-1 checksum
- `.sha256` - SHA-256 checksum
- `.sha512` - SHA-512 checksum

Example:
```
bearsampp-build/apps/phpmyadmin/2025.1.23/
├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z
├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.md5
├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.sha1
├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.sha256
└── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.sha512
```

### Download Sources

The build system automatically downloads phpMyAdmin from multiple sources with fallback:

1. **modules-untouched repository** (primary)
   - URL: `https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/phpmyadmin.properties`
   - Provides centralized version management

2. **releases.properties** (local fallback)
   - Local file in the repository
   - Used when modules-untouched is unavailable

3. **Standard URL format** (final fallback)
   - Constructs URL: `https://files.phpmyadmin.net/phpMyAdmin/{version}/phpMyAdmin-{version}-all-languages.7z`
   - Used when both above sources fail

### Caching

Downloaded phpMyAdmin archives are cached in `bearsampp-build/tmp/downloads/phpmyadmin/` to speed up subsequent builds. Use `gradle cleanAll` to clear the cache.

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
Bundle version not found in bin/ or bin/archived/
```

**Solution:**
1. List available versions: `gradle listVersions`
2. Use an existing version: `gradle release -PbundleVersion=5.2.1`
3. Create the version folder in `bin/` with required configuration files

---

#### Issue: "7-Zip not found"

**Symptom:**
```
7-Zip not found. Please install 7-Zip or set 7Z_HOME environment variable.
```

**Solution:**
1. Install 7-Zip from https://www.7-zip.org/
2. Or set `7Z_HOME` environment variable to your 7-Zip installation directory
3. Verify: `gradle verify`

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

#### Issue: "Could not fetch modules-untouched properties"

**Symptom:**
```
Could not fetch modules-untouched properties: Connection timeout
```

**Solution:**
This is a warning. The build will automatically fall back to:
1. Local `releases.properties` file
2. Standard URL format construction

No action needed unless all fallbacks fail.

---

### Debug Mode

Run Gradle with debug output:

```bash
gradle release -PbundleVersion=5.2.1 --info
gradle release -PbundleVersion=5.2.1 --debug
```

### Clean Build

If you encounter issues, try a clean build:

```bash
gradle cleanAll
gradle release -PbundleVersion=5.2.1
```

---

## Additional Resources

- [Gradle Documentation](https://docs.gradle.org/)
- [Bearsampp Project](https://github.com/bearsampp/bearsampp)
- [phpMyAdmin Official Site](https://www.phpmyadmin.net/)
- [phpMyAdmin Downloads](https://www.phpmyadmin.net/downloads/)

---

## Support

For issues and questions:

- **GitHub Issues**: https://github.com/bearsampp/module-phpmyadmin/issues
- **Bearsampp Issues**: https://github.com/bearsampp/bearsampp/issues
- **Documentation**: https://bearsampp.com/module/phpmyadmin

---

**Last Updated**: 2025-01-31  
**Version**: 2025.1.23  
**Build System**: Pure Gradle (no wrapper, no Ant)

**Notes**:
- This project deliberately does **not** ship the Gradle Wrapper. Install Gradle 8+ locally and run with `gradle ...`.
- All Ant-related files have been removed. The build system is **pure Gradle**.
- No wrapper scripts (`gradlew`, `gradlew.bat`) or wrapper directory (`gradle/wrapper/`) are included.
