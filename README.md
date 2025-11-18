<p align="center"><a href="https://bearsampp.com/contribute" target="_blank"><img width="250" src="img/Bearsampp-logo.svg"></a></p>

[![GitHub release](https://img.shields.io/github/release/bearsampp/module-phpmyadmin.svg?style=flat-square)](https://github.com/bearsampp/module-phpmyadmin/releases/latest)
![Total downloads](https://img.shields.io/github/downloads/bearsampp/module-phpmyadmin/total.svg?style=flat-square)

This is a module of [Bearsampp project](https://github.com/bearsampp/bearsampp) involving phpMyAdmin.

## Documentation and downloads

https://bearsampp.com/module/phpmyadmin

## Build Documentation

This module uses a **pure Gradle build system** for creating releases. For detailed information about building this module, including how version folders are structured and included in releases, see [.gradle-docs/](.gradle-docs/).

### Quick Start

```bash
# Build a specific version
gradle release -PbundleVersion=5.2.1

# Build all available versions
gradle releaseAll

# List available versions
gradle listVersions

# Verify build environment
gradle verify

# Display build information
gradle info
```

### Key Features

- ✅ **Version folder inclusion** - Archives contain version folder (e.g., `phpmyadmin5.2.1/`)
- ✅ **Automatic download** - Fetches phpMyAdmin from multiple sources with fallback
- ✅ **Hash generation** - Creates MD5, SHA1, SHA256, and SHA512 hash files
- ✅ **Build all versions** - Single command to build all versions in bin/
- ✅ **Pure Gradle** - No Ant dependencies, modern build system

### Documentation

Complete build documentation is available in [.gradle-docs/](.gradle-docs/):
- [README.md](.gradle-docs/README.md) - Main documentation and quick reference
- [BUILD.md](.gradle-docs/BUILD.md) - Comprehensive build documentation
- [VERSION_FOLDER_VERIFICATION.md](.gradle-docs/VERSION_FOLDER_VERIFICATION.md) - Version folder inclusion proof
- [CHANGES.md](.gradle-docs/CHANGES.md) - Summary of all changes

### Requirements

- Java 8 or higher
- Gradle 6.0 or higher
- 7-Zip (for archive creation)

## Issues

Issues must be reported on [Bearsampp repository](https://github.com/bearsampp/bearsampp/issues).
