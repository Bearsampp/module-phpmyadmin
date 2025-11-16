# Gradle Build Documentation

This directory contains comprehensive documentation for the Gradle build system used in module-phpmyadmin.

## Documentation Index

### [BUILD.md](BUILD.md)
Complete build documentation including:
- Build structure and version folder inclusion
- Build process flow
- Usage examples and commands
- Version management
- Configuration files
- Archive format details
- Troubleshooting guide

### [GRADLE_MIGRATION.md](GRADLE_MIGRATION.md)
Migration summary from Ant to pure Gradle:
- What changed in the migration
- Key features of the new build system
- Build output structure
- Verification steps
- Benefits of the migration

### [VERSION_FOLDER_VERIFICATION.md](VERSION_FOLDER_VERIFICATION.md)
Detailed verification that version folders are correctly included in archives:
- Implementation analysis
- Code correctness proof
- Comparison with module-bruno
- Verification steps
- Test results

### [CHANGES.md](CHANGES.md)
Summary of all changes made during the migration:
- Issue resolution
- Migration details
- Key features added
- Testing results
- Files created/modified

## Quick Reference

### Build Commands

```bash
# Interactive release (prompts for version)
gradle release

# Non-interactive release
gradle release -PbundleVersion=5.2.1

# Build all versions
gradle releaseAll

# Clean build artifacts
gradle clean

# Clean everything (including downloads)
gradle cleanAll

# Verify environment
gradle verify

# List available versions
gradle listVersions

# Display build info
gradle info
```

### Directory Structure

```
bearsampp-build/
├── tmp/
│   ├── downloads/phpmyadmin/          # Downloaded source archives (cached)
│   ├── extract/phpmyadmin/{version}/  # Temporary extraction
│   ├── bundles_prep/apps/phpmyadmin/  # Prepared bundles
│   └── bundles_build/apps/phpmyadmin/ # Final uncompressed output
└── apps/phpmyadmin/{release}/         # Final compressed archives + hashes
```

### Archive Structure

Archives correctly include the version folder:
```
phpmyadmin5.2.1-2025.1.23.7z
└── phpmyadmin5.2.1/
    ├── bearsampp.conf
    ├── config.inc.php
    ├── index.php
    └── [all phpMyAdmin files]
```

## Key Features

✅ **Interactive versioning** - Run `gradle release` and select from a menu  
✅ **Non-interactive mode** - Run `gradle release -PbundleVersion=5.2.1`  
✅ **Archived versions support** - Checks both `bin/` and `bin/archived/`  
✅ **Version folder inclusion** - Archives include version folder at root  
✅ **No deprecation warnings** - Clean build output  
✅ **Pure Gradle** - No Ant dependencies  
✅ **Automatic download** - Fetches from modules-untouched with fallback  
✅ **Hash generation** - MD5, SHA1, SHA256, SHA512  
✅ **Build all versions** - Single command to build everything  

## Build System Architecture

The build system uses a pure Gradle implementation that:
- Downloads phpMyAdmin from modules-untouched repository
- Extracts and prepares the bundle with custom configuration
- Creates both uncompressed (for development) and compressed (for distribution) outputs
- Generates hash files for verification
- Caches downloads to speed up subsequent builds
- Matches module-bruno's directory structure and behavior

## Support

For issues or questions:
- Check the troubleshooting sections in BUILD.md
- Review GRADLE_MIGRATION.md for migration-specific issues
- Verify your environment with `gradle verify`
- Report issues on [Bearsampp repository](https://github.com/bearsampp/bearsampp/issues)
