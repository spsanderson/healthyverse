# Contributing Guide

Thank you for your interest in contributing to the healthyverse! This guide will help you get started.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Pull Request Process](#pull-request-process)
- [Coding Standards](#coding-standards)
- [Documentation Guidelines](#documentation-guidelines)
- [Testing Guidelines](#testing-guidelines)
- [Community](#community)

---

## Code of Conduct

The healthyverse project is committed to providing a welcoming and inclusive environment for all contributors. We follow the [Contributor Covenant Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/).

### Our Standards

**Positive behavior includes**:
- Using welcoming and inclusive language
- Being respectful of differing viewpoints
- Gracefully accepting constructive criticism
- Focusing on what is best for the community
- Showing empathy towards other community members

**Unacceptable behavior includes**:
- Harassment, trolling, or discriminatory comments
- Publishing others' private information
- Other conduct which could reasonably be considered inappropriate

### Enforcement

Instances of unacceptable behavior may be reported to spsanderson@gmail.com. All complaints will be reviewed and investigated promptly and fairly.

---

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please:
1. Check the [existing issues](https://github.com/spsanderson/healthyverse/issues)
2. Run `healthyverse_sitrep()` to gather diagnostic information

**How to Submit a Bug Report**:

1. **Use a clear, descriptive title**
2. **Describe the exact steps to reproduce**:
   ```r
   # Minimal reproducible example
   library(healthyverse)
   
   # Code that produces the bug
   ```
3. **Describe the behavior you observed and what you expected**
4. **Include your system information**:
   ```r
   healthyverse_sitrep()
   sessionInfo()
   ```
5. **Add any relevant output, screenshots, or error messages**

### Suggesting Enhancements

Enhancement suggestions are tracked as [GitHub Issues](https://github.com/spsanderson/healthyverse/issues).

**How to Submit an Enhancement**:

1. **Use a clear, descriptive title**
2. **Provide a detailed description** of the proposed feature
3. **Explain why this enhancement would be useful**
4. **Provide examples** of how the feature would work:
   ```r
   # Example usage
   result <- proposed_function(data, options)
   ```
5. **List any alternative solutions** you've considered
6. **Reference any relevant documentation** or similar features in other packages

### Improving Documentation

Documentation improvements are always welcome!

**What you can improve**:
- Fix typos or clarify existing documentation
- Add examples to function documentation
- Create or improve vignettes
- Expand the wiki pages
- Add code comments

**How to contribute documentation**:
1. Fork the repository
2. Make your changes
3. Submit a pull request (see below)

### Contributing Code

See [Development Workflow](#development-workflow) below.

---

## Getting Started

### Prerequisites

- R (≥ 3.4.0, preferably 4.3.0+)
- RStudio (recommended)
- Git
- GitHub account
- Development tools (Rtools/Xcode/build-essential)

### Setting Up Development Environment

1. **Fork the repository**:
   - Go to https://github.com/spsanderson/healthyverse
   - Click "Fork" button

2. **Clone your fork**:
   ```bash
   git clone https://github.com/YOUR-USERNAME/healthyverse.git
   cd healthyverse
   ```

3. **Set up remotes**:
   ```bash
   git remote add upstream https://github.com/spsanderson/healthyverse.git
   git remote -v
   ```

4. **Install dependencies**:
   ```r
   # In R
   install.packages(c("devtools", "roxygen2", "testthat", "knitr"))
   devtools::install_deps()
   ```

5. **Load the package**:
   ```r
   devtools::load_all()
   ```

---

## Development Workflow

### 1. Create a Branch

```bash
# Update your fork
git checkout master
git pull upstream master

# Create feature branch
git checkout -b feature/your-feature-name
```

Branch naming conventions:
- `feature/` - New features
- `bugfix/` - Bug fixes
- `docs/` - Documentation changes
- `test/` - Test additions or fixes

### 2. Make Your Changes

**For code changes**:
- Write clean, readable code
- Follow the [Coding Standards](#coding-standards)
- Add or update tests
- Update documentation
- Test your changes thoroughly

**For documentation changes**:
- Use clear, concise language
- Include examples where appropriate
- Check for typos and formatting
- Ensure links work correctly

### 3. Document Your Code

Use roxygen2 for function documentation:

```r
#' Short function description
#'
#' Longer description providing more details about what the function does.
#' Can span multiple lines.
#'
#' @param x A numeric vector
#' @param y A character string describing the operation
#' @param ... Additional arguments passed to other methods
#'
#' @return Returns a modified version of x based on y
#'
#' @examples
#' \dontrun{
#' result <- my_function(1:10, "transform")
#' print(result)
#' }
#'
#' @export
my_function <- function(x, y, ...) {
  # Function implementation
}
```

### 4. Write Tests

Add tests in `tests/testthat/`:

```r
test_that("my_function works correctly", {
  result <- my_function(1:5, "test")
  
  expect_equal(length(result), 5)
  expect_type(result, "double")
  expect_true(all(result > 0))
})

test_that("my_function handles errors", {
  expect_error(my_function("invalid", "test"))
  expect_error(my_function(NULL, "test"))
})
```

### 5. Run Checks

Before committing:

```r
# Load the package
devtools::load_all()

# Run tests
devtools::test()

# Check package
devtools::check()

# Build documentation
devtools::document()
```

### 6. Commit Your Changes

Write clear commit messages:

```bash
git add .
git commit -m "Add feature: brief description

More detailed explanation of what changed and why.
Fixes #123"
```

Commit message guidelines:
- Use present tense ("Add feature" not "Added feature")
- Be concise but descriptive
- Reference issues with `Fixes #123` or `Closes #456`

### 7. Push to Your Fork

```bash
git push origin feature/your-feature-name
```

---

## Pull Request Process

### Before Submitting

Ensure:
- [ ] All tests pass (`devtools::test()`)
- [ ] Package check passes (`devtools::check()`)
- [ ] Documentation is updated
- [ ] Code follows style guidelines
- [ ] Commits have clear messages
- [ ] Branch is up to date with master

### Submitting a Pull Request

1. **Go to GitHub and create a pull request**

2. **Fill out the PR template**:
   - **Title**: Clear, concise description
   - **Description**: 
     - What does this PR do?
     - Why is this change needed?
     - What issues does it fix?
   - **Testing**: How was this tested?
   - **Checklist**: Complete the checklist items

3. **Example PR description**:
   ```markdown
   ## Description
   Adds new function `calculate_metrics()` for healthcare data analysis.
   
   ## Motivation
   Users frequently need to calculate common healthcare metrics. This function
   streamlines that process.
   
   ## Changes
   - Added `calculate_metrics()` function
   - Added tests for new function
   - Updated documentation
   - Added example to vignette
   
   ## Testing
   - Unit tests pass
   - Manual testing with sample data
   - Checked with `R CMD check`
   
   Fixes #123
   ```

### Review Process

1. **Automated checks** will run on your PR
2. **Maintainer will review** your code
3. **Address feedback**:
   ```bash
   # Make requested changes
   git add .
   git commit -m "Address review feedback"
   git push origin feature/your-feature-name
   ```
4. **Approval and merge**

---

## Coding Standards

### R Code Style

Follow the [tidyverse style guide](https://style.tidyverse.org/):

**Naming**:
```r
# Good
calculate_mean_value <- function(x) { }
user_data <- read.csv("data.csv")

# Bad
calcMeanValue <- function(x) { }
UserData <- read.csv("data.csv")
```

**Spacing**:
```r
# Good
x <- 5
y <- x + 2
result <- function(a, b) a + b

# Bad
x<-5
y<-x+2
result <- function(a,b)a+b
```

**Indentation**:
```r
# Good
if (condition) {
  do_something()
  do_another_thing()
}

# Bad
if (condition) {
do_something()
do_another_thing()
}
```

**Line length**: Keep lines ≤ 80 characters when possible

**Comments**:
```r
# Good: Explain why, not what
# Use log transformation to normalize skewed distribution
data_transformed <- log(data)

# Bad: States the obvious
# Take the log of data
data_transformed <- log(data)
```

### Package Dependencies

- Use `::` for explicit package references in code
- Minimize new dependencies
- Declare all dependencies in DESCRIPTION
- Use `Imports:` for required packages
- Use `Suggests:` for optional features

---

## Documentation Guidelines

### Function Documentation

Use roxygen2 with these required sections:
- `@param` - Document all parameters
- `@return` - Describe return value
- `@examples` - Provide working examples
- `@export` - For user-facing functions

### Vignettes

When adding vignettes:
- Use clear, instructive titles
- Include practical examples
- Explain the "why" not just the "how"
- Test all code chunks
- Keep examples reproducible

### README and Wiki

- Write for beginners
- Use clear headings
- Include examples
- Keep it updated
- Check links regularly

---

## Testing Guidelines

### Writing Tests

```r
# tests/testthat/test-function-name.R

test_that("function handles normal input", {
  result <- my_function(1:10)
  expect_equal(length(result), 10)
  expect_type(result, "double")
})

test_that("function validates input", {
  expect_error(my_function("invalid"))
  expect_error(my_function(NULL))
  expect_error(my_function(NA))
})

test_that("function edge cases", {
  expect_equal(my_function(numeric(0)), numeric(0))
  expect_equal(my_function(1), 1)
})
```

### Test Coverage

- Aim for high test coverage
- Test normal cases
- Test edge cases
- Test error handling
- Test with different data types

---

## Community

### Communication Channels

- **GitHub Issues**: Bug reports and feature requests
- **GitHub Discussions**: Questions and general discussion
- **Email**: spsanderson@gmail.com

### Recognition

Contributors are recognized in:
- Package DESCRIPTION file
- Release notes
- Annual contributor acknowledgments

---

## Additional Resources

- [R Packages Book](https://r-pkgs.org/)
- [Tidyverse Style Guide](https://style.tidyverse.org/)
- [Writing R Extensions](https://cran.r-project.org/doc/manuals/r-release/R-exts.html)
- [GitHub Flow](https://guides.github.com/introduction/flow/)

---

## Questions?

If you have questions about contributing:
1. Check this guide
2. Search [GitHub Discussions](https://github.com/spsanderson/healthyverse/discussions)
3. Ask in a new Discussion
4. Email the maintainer

---

Thank you for contributing to healthyverse! 🎉

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
