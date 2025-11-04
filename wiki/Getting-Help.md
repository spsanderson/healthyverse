# Getting Help

A guide to finding help and support for the healthyverse.

## Table of Contents

- [Before Asking for Help](#before-asking-for-help)
- [Documentation Resources](#documentation-resources)
- [Community Support](#community-support)
- [Reporting Issues](#reporting-issues)
- [Getting Diagnostic Information](#getting-diagnostic-information)
- [Common Questions](#common-questions)

---

## Before Asking for Help

### 1. Check the Documentation

Before asking for help, try these resources:

```r
# Function documentation
?function_name
help("function_name")

# Package documentation
?healthyverse
help(package = "healthyverse")

# Vignettes
browseVignettes("healthyverse")
vignette("getting-started", package = "healthyverse")

# See all available help
help.start()
```

### 2. Search Existing Resources

- **This Wiki**: Use the search function or browse pages
- **FAQ**: Check the [FAQ](FAQ) page
- **Troubleshooting**: See [Troubleshooting](Troubleshooting) guide
- **GitHub Issues**: Search [existing issues](https://github.com/spsanderson/healthyverse/issues)

### 3. Try to Solve It Yourself

```r
# Get system information
healthyverse_sitrep()
sessionInfo()

# Check for updates
healthyverse_update()

# Check for conflicts
healthyverse_conflicts()

# Look at examples
example(function_name)
```

---

## Documentation Resources

### Package Websites

Each healthyverse package has comprehensive documentation:

- **healthyverse**: https://www.spsanderson.com/healthyverse/
- **healthyR**: https://www.spsanderson.com/healthyR/
- **healthyR.data**: https://www.spsanderson.com/healthyR.data/
- **healthyR.ts**: https://www.spsanderson.com/healthyR.ts/
- **healthyR.ai**: https://www.spsanderson.com/healthyR.ai/
- **TidyDensity**: https://www.spsanderson.com/TidyDensity/
- **tidyAML**: https://www.spsanderson.com/tidyAML/
- **RandomWalker**: https://www.spsanderson.com/RandomWalker/

### CRAN Documentation

- CRAN page: https://cran.r-project.org/package=healthyverse
- PDF manual (comprehensive reference)
- Package vignettes

### Wiki Pages

This wiki contains extensive guides:
- [Quick Start Tutorial](Quick-Start-Tutorial)
- [Installation Guide](Installation-Guide)
- [Core Packages Overview](Core-Packages-Overview)
- [API Reference](API-Reference)
- And many more...

---

## Community Support

### GitHub Discussions

**Best for**: Questions, ideas, and general discussion

- **URL**: https://github.com/spsanderson/healthyverse/discussions
- **Categories**:
  - 💡 Ideas: Feature requests and suggestions
  - 🙏 Q&A: Ask questions
  - 🗨️ General: General discussion
  - 📣 Announcements: News and updates

**How to ask a good question**:

1. **Search first**: Check if your question was already asked
2. **Clear title**: "How do I forecast with multiple seasonality?"
3. **Provide context**: What are you trying to accomplish?
4. **Include code**: Share a minimal reproducible example
5. **System info**: Include output from `healthyverse_sitrep()`

### GitHub Issues

**Best for**: Bug reports and feature requests

- **URL**: https://github.com/spsanderson/healthyverse/issues
- **Use for**:
  - Reporting bugs
  - Requesting features
  - Documentation improvements

**Not for**:
- General questions (use Discussions instead)
- Help with your code (use Discussions or Stack Overflow)

### Stack Overflow

**Best for**: Programming questions

- Tag your question with `[r]` and `[healthyverse]`
- Include a reproducible example
- Show what you've tried

### Email

**Best for**: Private matters, security issues

- **Email**: spsanderson@gmail.com
- **Use for**:
  - Security vulnerabilities
  - Private/confidential matters
  - Licensing questions

---

## Reporting Issues

### Bug Reports

When reporting a bug, include:

1. **System information**:
   ```r
   healthyverse_sitrep()
   sessionInfo()
   ```

2. **Minimal reproducible example**:
   ```r
   library(healthyverse)
   
   # Simplest code that reproduces the bug
   # Use built-in datasets when possible
   ```

3. **Expected behavior**: What you expected to happen

4. **Actual behavior**: What actually happened

5. **Error messages**: Complete error text (if any)

### Example Bug Report

**Title**: "healthyverse_update() fails with proxy error"

**Description**:

I'm behind a corporate proxy and `healthyverse_update()` fails with a connection error.

**System Information**:
```
── R & RStudio ──────────────────────
• RStudio: 2023.12.1.402
• R: 4.3.2

── Core packages ────────────────────
• healthyverse (1.1.0)
```

**Reproducible Example**:
```r
library(healthyverse)
healthyverse_update()

# Error: cannot open URL 'https://...'
```

**Expected**: Should check for updates

**Actual**: Connection fails

**What I've tried**:
- Set proxy with `Sys.setenv(http_proxy = "...")`
- Checked internet connection (working)
- Updated R and RStudio

---

## Getting Diagnostic Information

### System Report

```r
# Comprehensive system information
healthyverse_sitrep()

# Output includes:
# - R version
# - RStudio version
# - All package versions
# - Packages that need updates
```

### Session Information

```r
# Detailed session information
sessionInfo()

# Or use devtools version (more readable)
devtools::session_info()

# Output includes:
# - R version
# - Platform details
# - Loaded packages
# - Locale settings
```

### Package Dependencies

```r
# Check dependencies
healthyverse_deps()

# Shows:
# - Package names
# - Installed versions
# - CRAN versions
# - Whether updates are available
```

### Conflicts

```r
# Check for function conflicts
healthyverse_conflicts()

# Shows which functions mask others
```

### Create a Reproducible Example

Use the `reprex` package:

```r
library(reprex)

# Copy your code, then run:
reprex({
  library(healthyverse)
  
  # Your code here
  result <- some_function(data)
})

# This creates a reproducible example you can share
```

---

## Common Questions

### "Where do I start?"

1. Read the [Quick Start Tutorial](Quick-Start-Tutorial)
2. Try the examples in `?healthyverse`
3. Browse vignettes with `browseVignettes("healthyverse")`

### "How do I do X?"

1. Check the [API Reference](API-Reference)
2. Look at package-specific wikis:
   - [Healthcare Data Analysis](Healthcare-Data-Analysis)
   - [Time Series Forecasting](Time-Series-Forecasting)
   - [Machine Learning Workflows](Machine-Learning-Workflows)
3. Search [GitHub Discussions](https://github.com/spsanderson/healthyverse/discussions)

### "Something's not working"

1. Check [Troubleshooting](Troubleshooting) guide
2. Run `healthyverse_sitrep()` to check versions
3. Update packages with `healthyverse_update()`
4. Restart R and try again

### "I found a bug"

1. Verify it's actually a bug (not expected behavior)
2. Check if already reported in [Issues](https://github.com/spsanderson/healthyverse/issues)
3. Create a minimal reproducible example
4. Submit a new issue with all details

### "I have a feature idea"

1. Check if already suggested in [Discussions](https://github.com/spsanderson/healthyverse/discussions)
2. Explain the use case and benefits
3. Provide examples of how it would work
4. Post in Ideas category or create an Issue

---

## Creating Good Examples

### Minimal Reproducible Example

```r
library(healthyverse)

# Use built-in data
data <- data.frame(
  x = 1:10,
  y = 11:20
)

# Minimal code that shows the problem
result <- some_function(data)
```

### Include Expected Output

```r
# What I expect
expected <- c(1, 2, 3)

# What I got
actual <- my_function()

# They don't match
identical(expected, actual)  # FALSE
```

### Use Dput for Data

```r
# Share data structure
dput(head(my_data, 5))

# Others can recreate with:
my_data <- structure(...)
```

---

## Response Times

**GitHub Discussions**: Usually within 1-3 days  
**GitHub Issues**: Usually within 1-5 days  
**Email**: Usually within 1 week

**Note**: This is a volunteer project. Please be patient and respectful.

---

## Helping Others

Once you're familiar with healthyverse, consider helping others:

1. **Answer questions** in GitHub Discussions
2. **Improve documentation** with pull requests
3. **Share examples** and use cases
4. **Write blog posts** or tutorials
5. **Report bugs** you encounter
6. **Test new features** in development versions

---

## Additional Resources

### R Help

- R documentation: `help.start()`
- R mailing lists: https://www.r-project.org/mail.html
- Stack Overflow `[r]` tag: https://stackoverflow.com/questions/tagged/r

### Learning R

- R for Data Science: https://r4ds.had.co.nz/
- Advanced R: https://adv-r.hadley.nz/
- Tidyverse: https://www.tidyverse.org/learn/

### Healthcare Analytics

- Package websites (listed above)
- This wiki's guides
- GitHub repository examples

---

## Remember

- **Be respectful** and patient
- **Search first** before asking
- **Provide details** when asking for help
- **Give back** by helping others when you can

The healthyverse community is here to help! 🎉

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
