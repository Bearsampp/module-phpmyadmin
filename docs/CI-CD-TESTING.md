# phpMyAdmin Module CI/CD Testing

## Overview

This document describes the automated testing workflow for the Bearsampp phpMyAdmin module. The CI/CD pipeline ensures that phpMyAdmin builds are properly packaged, can be extracted, and that all required files and dependencies are present and functional.

## Workflow Triggers

The phpMyAdmin testing workflow is triggered automatically on:

- **Pull Request Events**: When a PR is opened, synchronized (new commits pushed), reopened, or edited
  - Target branches: `main`
- **Manual Workflow Dispatch**: Can be triggered manually with options to test specific versions

## Test Scope

The workflow intelligently determines which versions to test based on the context:

### Pull Request Testing

- **Smart Detection**: Automatically detects which phpMyAdmin versions are included in the PR from pre-release
  - **Primary Method**: Extracts version numbers from changed files in `/bin` directory (e.g., `bin/phpmyadmin5.2.3/`)
  - **Fallback Method**: If no versions found in `/bin`, extracts version numbers from PR title
  - **Skip Behavior**: If no versions detected by either method, tests are skipped
- **Efficiency**: Reduces CI runtime by testing only relevant versions
- **Comprehensive**: Tests all versions including major and minor releases

**How it works:**

1. New versions are created and added to a pre-release (tagged with date, e.g., "2025.11.23")
2. Version directories are created in `/bin` (e.g., `bin/phpmyadmin5.2.3/`, `bin/phpmyadmin5.2.2/`)
3. The `releases.properties` file is updated via the releases.properties workflow
4. A PR is created from a release branch (e.g., "November") to `main`
5. This workflow detects changed files in `/bin` and extracts version numbers from directory names
6. Tests are run against the version(s) found in `releases.properties` from that PR branch

### Manual Testing
- **Latest 5 Versions**: Tests the 5 most recent versions from `releases.properties`
- **Specific Version**: Tests a single version provided as input parameter
- **Flexibility**: Useful for re-testing or validating specific versions

### Example Scenarios
- **Add phpMyAdmin 5.2.3**: Only version 5.2.3 is tested
- **Add versions 5.2.2 and 5.2.3**: Both versions are tested
- **PR with version in title (e.g., "Update docs for 5.2.3")**: Version 5.2.3 is tested
- **Non-version changes (e.g., documentation only)**: Tests are skipped

## Test Phases

### Phase 1: Installation Validation

**Purpose**: Verify the phpMyAdmin package can be downloaded and extracted correctly.

**Steps**:
1. **Download phpMyAdmin** (Phase 1.1)
   - Reads the download URL from `releases.properties`
   - Downloads the `.7z` archive from GitHub releases
   - Reports download size and success status
   - Validates file size (must be > 0.1 MB)

2. **Extract Archive** (Phase 1.1)
   - Extracts the `.7z` file using 7-Zip
   - Locates the `phpmyadmin*` subfolder
   - Validates the directory structure
   - Verifies required files exist (`index.php`, `config.sample.inc.php`)

3. **Verify Files & Directories** (Phase 1.2)
   - Checks for required files:
     - `index.php` - Main entry point
     - `config.sample.inc.php` - Sample configuration file
   - Checks for required directories:
     - `libraries/` - Core phpMyAdmin libraries
     - `templates/` - Template files
     - `themes/` - Theme files
     - `vendor/` - Composer dependencies
   - Attempts to detect version from `libraries/classes/Version.php`
   - Saves verification results to JSON

### PHP Setup

**Purpose**: Ensure PHP is available for testing phpMyAdmin functionality.

**Steps**:
1. **Install PHP** using `shivammathur/setup-php@v2` action
   - PHP Version: 8.2
   - Extensions: mysqli, mbstring, zip, gd, xml, json
   - Configuration: `post_max_size=256M`, `max_execution_time=180`
   - Tools: Composer included

### Phase 2: Basic Functionality

**Purpose**: Verify phpMyAdmin files are valid and dependencies are present.

**Steps**:
1. **Test PHP Files** (Phase 2)
   - Validates `index.php` contains valid PHP opening tag
   - Checks `config.sample.inc.php` contains configuration array (`$cfg`)
   - Verifies Composer autoload file exists (`vendor/autoload.php`)
   - Checks themes directory and lists available themes
   - Reports pass/fail status for each test

### Phase 3: Database Connectivity

**Purpose**: Test phpMyAdmin's web interface and ability to connect to a MySQL database through the application itself.

**Steps**:
1. **Setup MySQL Service**
   - Installs MySQL 8.0.33 via Chocolatey
   - Verifies MySQL service is running
   - Waits for service initialization

2. **Create Test Database**
   - Creates `phpmyadmin_test` database
   - Creates test user `pma_test` with password
   - Grants necessary privileges
   - Flushes privileges

3. **Configure phpMyAdmin**
   - Creates `config.inc.php` with test database credentials
   - Sets authentication type to 'config'
   - Configures connection parameters

4. **Start PHP Built-in Server**
   - Launches PHP development server on localhost:8080
   - Serves phpMyAdmin web interface
   - Runs in background for testing

5. **Test phpMyAdmin Web Interface**
   - Accesses phpMyAdmin via HTTP (http://localhost:8080/index.php)
   - Verifies HTTP 200 response
   - Validates phpMyAdmin content is present
   - Confirms interface loads correctly

6. **Test Database Operations Through phpMyAdmin**
   - Accesses database listing page via phpMyAdmin interface
   - Verifies phpMyAdmin can connect to MySQL
   - Checks if test database is visible in phpMyAdmin
   - Validates database operations work through the web interface

7. **Cleanup**
   - Stops PHP development server
   - Cleans up background processes

**Note**: Phase 3 is **mandatory** and must pass for the overall test to succeed. This ensures phpMyAdmin can actually function as a web application and connect to MySQL databases, which is its primary purpose.

## Test Results

### Artifacts

The workflow generates and uploads the following artifacts:

1. **Test Results** (`test-results-phpmyadmin-VERSION`)
   - `verify.json` - File and directory verification results
   - `summary.md` - Human-readable test summary
   - Retention: 30 days

### PR Comments

For pull requests, the workflow automatically posts a comment with:
- Test date and time (UTC)
- Summary for each tested version
- Pass/fail status for each phase
- Overall test status
- Troubleshooting tips for failures
- Links to detailed artifacts

The comment is updated if it already exists (no duplicate comments).

### Test Summary Format

Each version's summary includes:

```
### phpMyAdmin X.Y.Z

**Phase 1: Installation Validation**
- Download & Extract: ✅ PASS / ❌ FAIL
- Verify Files & Directories: ✅ PASS / ❌ FAIL

**Phase 2: Basic Functionality**
- Test PHP Files & Dependencies: ✅ PASS / ❌ FAIL

**Overall Status:** ✅ ALL TESTS PASSED / ❌ SOME TESTS FAILED
```

If tests fail, additional troubleshooting information is provided:
- Specific error messages
- Collapsible troubleshooting tips
- Links to workflow logs
- References to test artifacts

## Error Handling

The workflow provides comprehensive error reporting at multiple levels:

### Detailed Error Messages

Each phase captures and reports specific error information:

- **Download failures**: 
  - HTTP status codes and error messages
  - Attempted URLs
  - File size validation errors
  
- **Extraction failures**: 
  - 7-Zip exit codes and output logs
  - Directory listings showing actual structure
  - Expected vs. actual directory patterns
  
- **Missing files**: 
  - List of expected files and directories
  - Actual directory contents
  - Specific missing items highlighted
  
- **Validation failures**: 
  - PHP syntax check results
  - Configuration file validation
  - Dependency check results

### Error Propagation

- Each phase uses `continue-on-error: true` to allow subsequent phases to run
- Failed phases are clearly marked in the summary with ❌ indicators
- Error messages are included inline in test summaries
- All test results are uploaded regardless of success/failure
- Overall workflow status reflects any failures

### Troubleshooting Assistance

When tests fail, the summary includes:
- Specific error messages for each failed phase
- Collapsible troubleshooting tips section with:
  - Links to detailed workflow logs
  - Instructions to download test artifacts
  - Common issues and solutions
  - Archive structure validation tips
  - PHP file and dependency checks

## Platform

- **Runner**: `windows-latest`
- **Reason**: Consistent testing environment for Windows-based deployments
- **Tools**: Native Windows PowerShell, 7-Zip (pre-installed)
- **OS**: Windows Server (latest GitHub Actions runner)

## Matrix Strategy

- **Parallel Execution**: Tests run in parallel for different versions
- **Fail-Fast**: Disabled (`fail-fast: false`) to test all versions even if one fails
- **Dynamic Matrix**: Version list is generated dynamically based on trigger context
- **Resource Efficiency**: Only tests relevant versions to minimize CI time

## Workflow File Location

`.github/workflows/phpmyadmin-test.yml`

## Maintenance

### Adding New Test Cases

To add new functionality tests:

1. Add a new step after Phase 2
2. Use `continue-on-error: true` to prevent blocking other tests
3. Check previous phase success with `if: steps.PREVIOUS_STEP.outputs.success == 'true'`
4. Set output variable: `echo "success=true/false" >> $env:GITHUB_OUTPUT`
5. Update the test summary generation to include the new phase
6. Document the new phase in this file

### Modifying Version Selection

To change which versions are tested, edit the `Get phpMyAdmin Versions` step:

```bash
# Current: Latest 5 versions
VERSIONS=$(... | head -5 | ...)

# Example: Latest 3 versions
VERSIONS=$(... | head -3 | ...)

# Example: Latest 10 versions
VERSIONS=$(... | head -10 | ...)
```

### Adjusting Test Parameters

Key parameters that can be adjusted:

- **Download timeout**: `TimeoutSec 300` (5 minutes) in Phase 1.1
- **Minimum file size**: `0.1 MB` validation in Phase 1.1
- **Artifact retention**: `retention-days: 30` in Upload Test Results step

### Adding New Files/Directories to Verify

To verify additional phpMyAdmin files or directories:

1. Edit Phase 1.2 in the workflow file
2. Add the item to the `$requiredItems` hashtable:
   ```powershell
   $requiredItems = @{
       "index.php" = "file"
       "config.sample.inc.php" = "file"
       "libraries" = "directory"
       "templates" = "directory"
       "themes" = "directory"
       "vendor" = "directory"
       "NEW_FILE.php" = "file"  # Add new file
       "NEW_DIR" = "directory"   # Add new directory
   }
   ```
3. Optionally add specific tests in Phase 2

## Troubleshooting

### Common Issues

1. **Download Failures**
   - **Symptom**: Phase 1.1 fails with download error
   - **Causes**:
     - Invalid URL in `releases.properties`
     - GitHub release not published or deleted
     - Network connectivity issues
     - Rate limiting from GitHub
   - **Solutions**:
     - Verify the release URL is accessible in a browser
     - Check GitHub releases page for the module
     - Ensure the version number matches exactly
     - Wait and retry if rate limited

2. **Extraction Failures**
   - **Symptom**: Phase 1.1 fails after successful download
   - **Causes**:
     - Corrupted `.7z` file
     - Incorrect archive structure
     - Insufficient disk space
   - **Solutions**:
     - Re-download and re-upload the release
     - Verify archive structure locally before uploading
     - Check that archive contains `phpmyadmin*` directory with required files

3. **Missing Files or Directories**
   - **Symptom**: Phase 1.2 fails with missing items
   - **Causes**:
     - Incomplete phpMyAdmin build
     - Wrong directory structure in archive
     - Missing Composer dependencies
   - **Solutions**:
     - Verify all required files and directories are present
     - Check that the build is complete and not a partial package
     - Ensure Composer dependencies are installed (`composer install --no-dev`)

4. **PHP File Validation Failures**
   - **Symptom**: Phase 2 fails when validating PHP files
   - **Causes**:
     - Corrupted PHP files
     - Missing PHP opening tags
     - Invalid configuration file
   - **Solutions**:
     - Verify PHP files are not corrupted
     - Check that files contain proper PHP syntax
     - Test the build locally before uploading

5. **Missing Composer Dependencies**
   - **Symptom**: Phase 2 fails on vendor/autoload.php check
   - **Causes**:
     - Composer dependencies not installed
     - `vendor/` directory not included in archive
   - **Solutions**:
     - Run `composer install --no-dev` before packaging
     - Ensure `vendor/` directory is included in the archive
     - Verify `vendor/autoload.php` exists

### Debugging Steps

To debug a specific version failure:

1. **Check Workflow Logs**
   - Navigate to the failed workflow run in GitHub Actions
   - Expand the failed step to see detailed output
   - Look for specific error messages

2. **Download Artifacts**
   - Download the `test-results-phpmyadmin-VERSION` artifact
   - Review `verify.json` for file/directory validation details
   - Check `summary.md` for formatted results

3. **Local Testing**
   - Download the same `.7z` file from releases
   - Extract it locally on Windows
   - Verify all required files and directories are present
   - Check for any missing dependencies

4. **Compare with Working Versions**
   - Compare directory structure with a passing version
   - Check file sizes and contents
   - Verify all dependencies are present

### Manual Workflow Testing

To manually test a specific version:

1. Go to the Actions tab in GitHub
2. Select "phpMyAdmin Module Tests" workflow
3. Click "Run workflow"
4. Choose "Specific version" from the dropdown
5. Enter the version number (e.g., "5.2.3")
6. Click "Run workflow"
7. Monitor the test results

## Best Practices

1. **Always test before merging**: The workflow runs on PRs to catch issues early
2. **Review test summaries**: Check PR comments for test results before merging
3. **Investigate failures**: Don't merge if tests fail without understanding why
4. **Keep versions updated**: Regularly update `releases.properties` with new phpMyAdmin versions
5. **Monitor artifact storage**: Old artifacts are automatically cleaned up after retention period
6. **Test locally first**: Before creating a PR, test the phpMyAdmin build locally
7. **Document changes**: Update this documentation when modifying the workflow
8. **Version consistency**: Ensure version numbers in filenames match `releases.properties`
9. **Include all dependencies**: Always run `composer install --no-dev` before packaging
10. **Verify archive structure**: Ensure the archive contains the correct directory structure

## Version Naming Conventions

phpMyAdmin versions in `releases.properties` should follow these patterns:

- **Major.Minor.Patch**: `5.2.3`, `5.2.2`, `5.2.1`
- **Major.Minor**: `5.2`, `4.9`
- **Legacy versions**: `4.9.10`

The workflow supports all version formats and will test them accordingly.

## Workflow Outputs

### Success Indicators

When all tests pass, you'll see:
- ✅ Green checkmark on the PR
- "All tests passed" status in PR comment
- All phases marked with ✅ PASS
- Artifacts uploaded successfully

### Failure Indicators

When tests fail, you'll see:
- ❌ Red X on the PR
- "Some tests failed" status in PR comment
- Failed phases marked with ❌ FAIL
- Error messages in the summary
- Troubleshooting tips displayed

## phpMyAdmin-Specific Considerations

### Required Files and Directories

phpMyAdmin requires the following structure:

```
phpmyadmin-X.Y.Z/
├── index.php                    # Main entry point
├── config.sample.inc.php        # Sample configuration
├── libraries/                   # Core libraries
│   └── classes/
│       └── Version.php          # Version information
├── templates/                   # Template files
├── themes/                      # Theme files
│   ├── pmahomme/               # Default theme
│   └── ...                     # Other themes
└── vendor/                      # Composer dependencies
    └── autoload.php            # Composer autoloader
```

### Composer Dependencies

phpMyAdmin uses Composer for dependency management. The workflow verifies:
- `vendor/` directory exists
- `vendor/autoload.php` exists
- Dependencies are properly installed

**Important**: Always run `composer install --no-dev` before packaging to ensure all required dependencies are included without development dependencies.

### Themes

phpMyAdmin includes multiple themes. The workflow:
- Verifies the `themes/` directory exists
- Lists all available themes
- Ensures at least one theme is present

### Configuration

The workflow checks for `config.sample.inc.php` which contains:
- Database connection settings template
- phpMyAdmin configuration options
- Security settings

## Related Documentation

- [phpMyAdmin Official Documentation](https://docs.phpmyadmin.net/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Bearsampp Project](https://github.com/Bearsampp)
- [Module phpMyAdmin Repository](https://github.com/Bearsampp/module-phpmyadmin)

## Changelog

### Version History

- **Initial Release**: Basic download and extraction testing with file/directory verification

### Future Enhancements

Potential improvements for future versions:

1. **PHP Syntax Validation**: Add PHP linting to validate all PHP files
2. **Configuration Testing**: Test configuration file parsing
3. **Database Connection Testing**: Test database connectivity (with mock database)
4. **Theme Validation**: Verify theme files are complete and valid
5. **Security Checks**: Verify default security settings
6. **Performance Benchmarks**: Add basic performance metrics
7. **Localization Testing**: Verify language files are present

## Contributing to Tests

To contribute improvements to the testing workflow:

1. Fork the repository
2. Create a feature branch
3. Modify `.github/workflows/phpmyadmin-test.yml`
4. Update this documentation
5. Test your changes with manual workflow dispatch
6. Submit a pull request with:
   - Description of changes
   - Rationale for improvements
   - Test results showing the changes work

## Support and Contact

For questions or issues with the CI/CD workflow:

- **GitHub Issues**: [Open an issue](https://github.com/Bearsampp/module-phpmyadmin/issues)
- **Discussions**: Use GitHub Discussions for questions
- **Pull Requests**: Submit improvements via PR

When reporting issues, please include:
- Version number being tested
- Link to failed workflow run
- Error messages from logs
- Steps to reproduce (if applicable)

## Packaging Guidelines

When creating a new phpMyAdmin release for Bearsampp:

1. **Download phpMyAdmin**: Get the official release from phpMyAdmin.net
2. **Install Dependencies**: Run `composer install --no-dev`
3. **Verify Structure**: Ensure all required files and directories are present
4. **Create Archive**: Package as `.7z` with naming convention `bearsampp-phpmyadmin-X.Y.Z-YYYY.MM.DD.7z`
5. **Upload to GitHub**: Create a release and upload the archive
6. **Update releases.properties**: Add the new version entry
7. **Create PR**: Submit PR to trigger automated testing
8. **Review Results**: Check CI/CD test results before merging

## Testing Checklist

Before merging a PR with new phpMyAdmin versions:

- [ ] Archive downloads successfully
- [ ] Archive extracts without errors
- [ ] All required files are present
- [ ] All required directories are present
- [ ] PHP files contain valid syntax
- [ ] Configuration file is valid
- [ ] Composer dependencies are installed
- [ ] At least one theme is present
- [ ] Version can be detected
- [ ] All CI/CD tests pass
- [ ] Test artifacts are available for review
