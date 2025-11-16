# Build Documentation for module-phpmyadmin

## Overview

This module uses a pure Gradle build system. The build process downloads phpMyAdmin releases, configures them, and packages them for Bearsampp.

## Build Structure

### Version Folder Inclusion

When building a release, the build system **includes the version folder** in the compressed archive. This is consistent with other Bearsampp modules (e.g., module-bruno).

**Example structure in the final archive:**
```
phpmyadmin5.2.1/
├── bearsampp.conf
├── config.inc.php
└── [phpMyAdmin files]
```

### How It Works

1. **Bundle Folder Naming**: The `${bundle.folder}` variable is derived from the bundle path and includes the version:
   - Example: `phpmyadmin5.2.1`

2. **Preparation Directory**: Files are copied to `${bundle.tmp.prep.path}/${bundle.folder}`:
   - This creates: `tmp/phpmyadmin5.2.1/[files]`

3. **Compression**: The build system compresses the entire folder structure, preserving the version folder:
   - Archive contains: `phpmyadmin5.2.1/` as the root directory

### Build Process Flow

1. **Version Validation**: Checks if the specified version exists in `bin/` directory
2. **Preparation**: Creates temporary directory structure with version folder name
3. **Download**: Fetches phpMyAdmin from:
   - modules-untouched repository (primary)
   - releases.properties (fallback)
   - Standard URL format (final fallback)
4. **Extraction**: Extracts phpMyAdmin archive using 7-Zip
5. **Configuration**: Copies custom configuration files from `bin/[version]/`
6. **Archiving**: Creates 7z archive with version folder included
7. **Hashing**: Generates MD5, SHA1, SHA256, and SHA512 hash files

## Building a Release

### Using Gradle

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

## Version Management

### Supported Versions

Versions are defined in `releases.properties`. Each entry maps a version to its download URL:

```properties
5.2.1 = https://files.phpmyadmin.net/phpMyAdmin/5.2.1/phpMyAdmin-5.2.1-all-languages.7z
5.2.0 = https://files.phpmyadmin.net/phpMyAdmin/5.2.0/phpMyAdmin-5.2.0-all-languages.7z
```

### Version Folder Structure

Each version has its own folder in `bin/`:

```
bin/
├── phpmyadmin5.2.1/
│   ├── bearsampp.conf
│   └── config.inc.php
├── phpmyadmin5.2.0/
│   ├── bearsampp.conf
│   └── config.inc.php
└── phpmyadmin4.9.10/
    ├── bearsampp.conf
    └── config.inc.php
```

## Configuration Files

### build.properties

Main build configuration:

```properties
bundle.name = phpmyadmin
bundle.release = 2025.1.23
bundle.type = apps
bundle.format = 7z
```

### releases.properties

Maps versions to download URLs for phpMyAdmin releases.

## Archive Format

The final archive (`.7z` format by default) contains:

```
phpmyadmin[VERSION]/
├── bearsampp.conf          # Bearsampp configuration
├── config.inc.php          # phpMyAdmin configuration
├── index.php               # phpMyAdmin entry point
├── [all phpMyAdmin files]  # Complete phpMyAdmin installation
└── ...
```

## Comparison with Other Modules

This build structure is consistent with other Bearsampp modules:

- **module-bruno**: Uses pure Gradle build with version folder inclusion
- **module-apache**: Uses similar pattern with version folder inclusion
- **module-php**: Uses similar pattern with version folder inclusion

All modules ensure the version folder is included in the compressed archive for proper organization and version management within Bearsampp.

## Key Features

### Pure Gradle Implementation

- No Ant dependencies required
- Modern build system with caching and incremental builds
- Parallel execution support
- Better IDE integration

### Automatic Download

The build system automatically downloads phpMyAdmin releases from:
1. **modules-untouched repository** (primary source)
2. **releases.properties** (local fallback)
3. **Standard URL format** (final fallback)

### Version Folder Inclusion

**Critical**: The build system ensures the version folder is included in the archive. This is verified by:
- Creating prep directory with version folder: `tmp/prep/phpmyadmin5.2.1/`
- Archiving from parent directory to include folder structure
- Final archive contains: `phpmyadmin5.2.1/[files]`

This matches the pattern used in module-bruno and ensures proper integration with Bearsampp.

## Troubleshooting

### Version Folder Not Found

If you get an error about a missing version folder:

1. Check that the version exists in `bin/`:
   ```bash
   gradle listVersions
   ```

2. Verify the version is defined in `releases.properties`:
   ```bash
   gradle listReleases
   ```

3. Create the version folder structure:
   ```bash
   mkdir bin/phpmyadmin[VERSION]
   # Add bearsampp.conf and config.inc.php
   ```

### Build Environment Issues

Run the verification task to check your environment:

```bash
gradle verify
```

This checks for:
- Java 8+ installation
- Required build files (build.properties)
- bin/ directory existence
- 7-Zip installation

### Archive Structure Verification

To verify the version folder is included in the archive:

```bash
# Extract and check structure
7z l phpmyadmin5.2.1-2025.1.23.7z

# Should show:
# phpmyadmin5.2.1/
# phpmyadmin5.2.1/index.php
# phpmyadmin5.2.1/config.inc.php
# etc.
```

## References

- [Bearsampp Project](https://github.com/bearsampp/bearsampp)
- [module-bruno build.gradle](https://github.com/Bearsampp/module-bruno/blob/gradle-convert/build.gradle) - Reference implementation
- [phpMyAdmin Official Site](https://www.phpmyadmin.net/)
- [Gradle Documentation](https://docs.gradle.org/)

## Migration from Ant

This module has been migrated from Ant to pure Gradle. Key changes:

- **No Ant import**: Pure Gradle implementation
- **Simplified build**: Single build.gradle file
- **Better performance**: Gradle caching and incremental builds
- **Modern tooling**: Better IDE support and debugging

The build output and archive structure remain identical to the Ant version, ensuring backward compatibility with Bearsampp.
