# Build Documentation for module-phpmyadmin

## Overview

This module uses a pure Gradle build system. The build process downloads phpMyAdmin releases, configures them, and packages them for Bearsampp.

## Build Structure

### Version Folder Inclusion

When building a release, the build system **includes the version folder** in the compressed archive. This is consistent with other Bearsampp modules (e.g., module-bruno, module-php).

**Example structure in the final archive:**
```
phpmyadmin5.2.1/
├── bearsampp.conf
├── config.inc.php
├── index.php
└── [phpMyAdmin files]
```

### How It Works

1. **Bundle Folder Naming**: The bundle folder is derived from the bundle path and includes the version:
   - Example: `phpmyadmin5.2.1`

2. **Preparation Directory**: Files are copied to `${bundleTmpPrepPath}/${bundleName}${bundleVersion}`:
   - This creates: `tmp/bundles_prep/apps/phpmyadmin/phpmyadmin5.2.1/[files]`

3. **Compression**: The build system compresses from the parent directory, preserving the version folder:
   - Archive contains: `phpmyadmin5.2.1/` as the root directory

### Build Process Flow

1. **Version Validation**: Checks if the specified version exists in `bin/` or `bin/archived/` directory
2. **Preparation**: Creates temporary directory structure with version folder name
3. **Download**: Fetches phpMyAdmin from:
   - modules-untouched repository (primary)
   - releases.properties (fallback)
   - Standard URL format (final fallback)
4. **Extraction**: Extracts phpMyAdmin archive using 7-Zip
5. **Configuration**: Copies custom configuration files from `bin/[version]/`
6. **Build Copy**: Copies to bundles_build directory (uncompressed for development/testing)
7. **Archiving**: Creates 7z archive with version folder included
8. **Hashing**: Generates MD5, SHA1, SHA256, and SHA512 hash files

## Building a Release

### Using Gradle

```bash
# Build a specific version (interactive mode)
gradle release

# Build a specific version (non-interactive mode)
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

Versions are defined in `releases.properties` and can also be fetched from the modules-untouched repository. Each entry maps a version to its download URL:

```properties
5.2.1 = https://files.phpmyadmin.net/phpMyAdmin/5.2.1/phpMyAdmin-5.2.1-all-languages.7z
5.2.0 = https://files.phpmyadmin.net/phpMyAdmin/5.2.0/phpMyAdmin-5.2.0-all-languages.7z
```

### Version Folder Structure

Each version has its own folder in `bin/` or `bin/archived/`:

```
bin/
├── phpmyadmin5.2.1/
│   ├── bearsampp.conf
│   └── config.inc.php
├── phpmyadmin5.2.0/
│   ├── bearsampp.conf
│   └── config.inc.php
└── archived/
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

Maps versions to download URLs for phpMyAdmin releases. This serves as a local fallback when the modules-untouched repository is unavailable.

## Archive Format

The final archive (`.7z` format by default) contains:

```
phpmyadmin[VERSION]/
├── bearsampp.conf          # Bearsampp configuration
├── config.inc.php          # phpMyAdmin configuration
├── index.php               # phpMyAdmin entry point
├── libraries/              # phpMyAdmin libraries
├── themes/                 # phpMyAdmin themes
└── [all phpMyAdmin files]  # Complete phpMyAdmin installation
```

## Comparison with Other Modules

This build structure is consistent with other Bearsampp modules:

- **module-bruno**: Uses pure Gradle build with version folder inclusion
- **module-php**: Uses pure Gradle build with version folder inclusion
- **module-apache**: Uses similar pattern with version folder inclusion

All modules ensure the version folder is included in the compressed archive for proper organization and version management within Bearsampp.

## Key Features

### Pure Gradle Implementation

- No external dependencies required (except 7-Zip for compression)
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
- Creating prep directory with version folder: `tmp/bundles_prep/apps/phpmyadmin/phpmyadmin5.2.1/`
- Archiving from parent directory to include folder structure
- Final archive contains: `phpmyadmin5.2.1/[files]`

This matches the pattern used in module-bruno and module-php, ensuring proper integration with Bearsampp.

### Interactive and Non-Interactive Modes

The build system supports both interactive and non-interactive modes:

**Interactive Mode**:
```bash
gradle release
```
- Displays a menu of available versions
- Allows selection by index or version string
- Shows version locations (bin/ or bin/archived/)

**Non-Interactive Mode**:
```bash
gradle release -PbundleVersion=5.2.1
```
- Directly builds the specified version
- Suitable for CI/CD pipelines
- No user interaction required

### Build All Versions

Build all available versions with a single command:
```bash
gradle releaseAll
```
- Builds all versions found in bin/ and bin/archived/
- Provides summary of successful and failed builds
- Continues on error to build remaining versions

## Troubleshooting

### Version Folder Not Found

If you get an error about a missing version folder:

1. Check that the version exists in `bin/` or `bin/archived/`:
   ```bash
   gradle listVersions
   ```

2. Verify the version is defined in `releases.properties` or modules-untouched:
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
# List archive contents
7z l bearsampp-phpmyadmin-5.2.1-2025.1.23.7z

# Should show:
# phpmyadmin5.2.1/
# phpmyadmin5.2.1/index.php
# phpmyadmin5.2.1/config.inc.php
# etc.
```

### Download Issues

If phpMyAdmin download fails:

1. Check network connectivity
2. Verify the version exists in modules-untouched:
   ```bash
   gradle checkModulesUntouched
   ```
3. Add the version to `releases.properties` as a fallback
4. The build system will automatically try multiple sources

### 7-Zip Not Found

If 7-Zip is not found:

1. Install 7-Zip from https://www.7-zip.org/
2. Or set the `7Z_HOME` environment variable to your 7-Zip installation directory
3. Verify installation:
   ```bash
   gradle verify
   ```

## Build Output Structure

### Temporary Build Files

Located in `bearsampp-build/tmp/`:

```
bearsampp-build/tmp/
├── downloads/phpmyadmin/          # Downloaded archives (cached)
│   └── phpMyAdmin-5.2.1-all-languages.7z
├── extract/phpmyadmin/            # Temporary extraction
│   └── 5.2.1/
├── bundles_prep/apps/phpmyadmin/  # Prepared bundles
│   └── phpmyadmin5.2.1/
└── bundles_build/apps/phpmyadmin/ # Final uncompressed output
    └── phpmyadmin5.2.1/
```

### Final Archives

Located in `bearsampp-build/apps/phpmyadmin/{bundle.release}/`:

```
bearsampp-build/apps/phpmyadmin/2025.1.23/
├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z
├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.md5
├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.sha1
├── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.sha256
└── bearsampp-phpmyadmin-5.2.1-2025.1.23.7z.sha512
```

## References

- [Bearsampp Project](https://github.com/bearsampp/bearsampp)
- [module-bruno build.gradle](https://github.com/Bearsampp/module-bruno/blob/gradle-convert/build.gradle) - Reference implementation
- [module-php build.gradle](https://github.com/Bearsampp/module-php/blob/gradle-convert/build.gradle) - Reference implementation
- [phpMyAdmin Official Site](https://www.phpmyadmin.net/)
- [Gradle Documentation](https://docs.gradle.org/)
