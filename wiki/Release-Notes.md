# Release Notes

Complete changelog and version history for the healthyverse.

## Table of Contents

- [Current Development Version](#current-development-version)
- [Version 1.1.0](#version-110)
- [Version 1.0.3](#version-103)
- [Version 1.0.2](#version-102)
- [Version 1.0.1](#version-101)
- [Version 1.0.0](#version-100)
- [Pre-Release Versions](#pre-release-versions)

---

## Current Development Version

**Version**: 1.1.0.9000 (Development)  
**Status**: In Development  
**Release Date**: TBD

### What's New

Development version with ongoing improvements and bug fixes.

### Changes

- Documentation improvements
- Bug fixes
- Performance enhancements

### Installation

```r
# Install development version
devtools::install_github("spsanderson/healthyverse")
```

---

## Version 1.1.0

**Release Date**: 2024  
**Status**: Current Stable Release

### Major Changes

#### New Package Added
- **RandomWalker** (0.1.0): Added to core healthyverse packages
  - Random walk simulations
  - Stochastic process modeling
  - Statistical analysis tools

### Enhancements

- Updated minimum versions for core packages
- Improved package loading and attachment process
- Enhanced conflict detection and reporting
- Better error messages and warnings

### Core Package Versions

| Package | Version |
|---------|---------|
| healthyR | ≥ 0.2.2 |
| healthyR.data | ≥ 1.1.1 |
| healthyR.ts | ≥ 0.3.0 |
| healthyR.ai | ≥ 0.1.0 |
| TidyDensity | ≥ 1.5.0 |
| tidyAML | ≥ 0.0.5 |
| RandomWalker | ≥ 0.1.0 |

### Bug Fixes

- Fixed issue with package attachment order
- Resolved conflict detection edge cases
- Improved compatibility with R 4.3+

### Documentation

- Updated README with RandomWalker information
- Expanded vignettes with more examples
- Improved function documentation

### Installation

```r
install.packages("healthyverse")
```

---

## Version 1.0.3

**Release Date**: 2023  
**Status**: Superseded

### Major Changes

#### New Package Added
- **tidyAML** (0.0.5): Added to core healthyverse packages
  - Automated machine learning framework
  - Built on tidymodels
  - Multiple model training and comparison

### Enhancements

- Improved startup messages
- Better conflict management
- Updated dependencies

### Core Package Versions

| Package | Version |
|---------|---------|
| healthyR | ≥ 0.2.1 |
| healthyR.data | ≥ 1.1.0 |
| healthyR.ts | ≥ 0.2.9 |
| healthyR.ai | ≥ 0.1.0 |
| TidyDensity | ≥ 1.4.0 |
| tidyAML | ≥ 0.0.5 |

### Installation

```r
# Install from archive
devtools::install_version("healthyverse", version = "1.0.3")
```

---

## Version 1.0.2

**Release Date**: 2023  
**Status**: Superseded

### Major Changes

#### New Package Added
- **TidyDensity** (1.4.0): Added to core healthyverse packages
  - Statistical distribution generation
  - Tidy format distributions
  - Visualization tools

### Enhancements

- Added `healthyverse_sitrep()` function for system diagnostics
- Improved package update checking
- Better handling of missing dependencies

### Bug Fixes

- Fixed installation issues with certain R versions
- Resolved namespace conflicts
- Improved error handling

### Installation

```r
# Install from archive
devtools::install_version("healthyverse", version = "1.0.2")
```

---

## Version 1.0.1

**Release Date**: 2022  
**Status**: Superseded

### Major Changes

#### New Package Added
- **healthyR.ai** (0.1.0): Added to core healthyverse packages
  - AI and machine learning utilities
  - Healthcare-specific ML functions
  - Model building and evaluation tools

### Enhancements

- Improved documentation
- Better conflict detection
- Enhanced startup messages

### Core Package Versions

| Package | Version |
|---------|---------|
| healthyR | ≥ 0.2.0 |
| healthyR.data | ≥ 1.0.0 |
| healthyR.ts | ≥ 0.2.5 |
| healthyR.ai | ≥ 0.1.0 |

### Installation

```r
# Install from archive
devtools::install_version("healthyverse", version = "1.0.1")
```

---

## Version 1.0.0

**Release Date**: 2022  
**Status**: First CRAN Release

### Major Changes

- 🎉 **First CRAN release**
- Established core package ecosystem
- Complete meta-package functionality

### Features

- Automatic loading of core packages
- Conflict detection and reporting
- Update checking functionality
- Dependency management tools

### Core Packages (Initial Release)

| Package | Version |
|---------|---------|
| healthyR | ≥ 0.1.0 |
| healthyR.data | ≥ 0.1.0 |
| healthyR.ts | ≥ 0.2.0 |

### Functions

- `healthyverse_packages()`: List all healthyverse packages
- `healthyverse_deps()`: Check dependencies
- `healthyverse_update()`: Update packages
- `healthyverse_conflicts()`: Show function conflicts

### Installation

```r
install.packages("healthyverse")
```

---

## Pre-Release Versions

### Version 0.0.0.9000

**Status**: Development  
**Date**: 2021-2022

#### Initial Development

- Project conception and planning
- Basic package structure
- Initial implementation of core functions
- Testing and refinement

#### Milestones

1. **Package scaffolding**: Basic structure and organization
2. **Core functions**: Implementation of main functionality
3. **Documentation**: Initial documentation and examples
4. **Testing**: Unit tests and integration tests
5. **CRAN preparation**: Package checks and submission

---

## Migration Guides

### Upgrading from 1.0.x to 1.1.0

**New Features**:
- RandomWalker package now included
- No breaking changes

**Action Required**:
```r
# Update healthyverse
update.packages("healthyverse")

# Load to get RandomWalker
library(healthyverse)
```

### Upgrading from 0.x to 1.0.0

**Breaking Changes**:
- Minimum R version changed to 3.4.0
- Some function arguments renamed for consistency

**Action Required**:
1. Update R if < 3.4.0
2. Update all healthyverse packages
3. Review code for deprecated function calls

---

## Future Plans

See [Roadmap](Roadmap) for upcoming features and releases.

### Planned for Next Release

- Performance improvements
- Additional helper functions
- Enhanced documentation
- More vignettes and examples

### Long-term Goals

- Expand package ecosystem
- Improve interoperability
- Add more healthcare-specific tools
- Enhance automation features

---

## Version Support

### Current Support

- **1.1.0**: Fully supported, receives updates
- **1.0.x**: Maintenance mode, critical fixes only
- **0.x**: No longer supported

### R Version Compatibility

| healthyverse Version | Minimum R Version | Recommended R Version |
|---------------------|-------------------|---------------------|
| 1.1.0 | 3.4.0 | 4.3.0+ |
| 1.0.x | 3.4.0 | 4.1.0+ |
| 0.x | 3.3.0 | 4.0.0+ |

---

## Getting Older Versions

### From CRAN Archive

```r
# Install specific version
devtools::install_version("healthyverse", version = "1.0.3")
```

### From GitHub Tags

```r
# Install from git tag
devtools::install_github("spsanderson/healthyverse@v1.0.3")
```

---

## Reporting Issues

Found a bug in a specific version?

1. Check if it's fixed in the latest version
2. Search [existing issues](https://github.com/spsanderson/healthyverse/issues)
3. Report with version information:
   ```r
   packageVersion("healthyverse")
   healthyverse_sitrep()
   ```

---

## Contributing

See [Contributing Guide](Contributing-Guide) for information on:
- Reporting bugs
- Suggesting features
- Submitting pull requests
- Development workflow

---

## Stay Updated

- **Watch** the [GitHub repository](https://github.com/spsanderson/healthyverse)
- **Check** [NEWS.md](https://github.com/spsanderson/healthyverse/blob/master/NEWS.md) for changes
- **Follow** updates in [GitHub Discussions](https://github.com/spsanderson/healthyverse/discussions)

---

**Last Updated**: November 2025  
**Package Version**: 1.1.0.9000
