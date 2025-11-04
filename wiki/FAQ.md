# Frequently Asked Questions (FAQ)

Common questions and answers about the healthyverse.

## Table of Contents

- [General Questions](#general-questions)
- [Installation & Setup](#installation--setup)
- [Package Management](#package-management)
- [Conflicts & Errors](#conflicts--errors)
- [Usage & Features](#usage--features)
- [Performance](#performance)
- [Contributing](#contributing)

---

## General Questions

### What is the healthyverse?

The healthyverse is a collection of R packages designed for healthcare data analysis, time-series forecasting, machine learning, and statistical modeling. It's inspired by the tidyverse and provides a consistent API across all packages.

### Why should I use healthyverse instead of individual packages?

The healthyverse meta-package:
- Loads all core packages with one command
- Automatically checks for conflicts
- Provides tools to manage and update all packages together
- Ensures version compatibility across packages
- Simplifies dependency management

### Is healthyverse free?

Yes! The healthyverse is open-source and released under the MIT license. It's completely free to use for any purpose, including commercial applications.

### Who maintains the healthyverse?

The healthyverse is created and maintained by Steven P. Sanderson II, MPH. It's an actively developed project with regular updates and community contributions.

---

## Installation & Setup

### How do I install healthyverse?

From CRAN (stable version):
```r
install.packages("healthyverse")
```

From GitHub (development version):
```r
devtools::install_github("spsanderson/healthyverse")
```

See the [Installation Guide](Installation-Guide) for more details.

### What R version do I need?

- **Minimum**: R 3.4.0
- **Recommended**: R 4.3.0 or higher
- **For native pipe**: R 4.1.0 or higher

### Do I need RStudio?

No, RStudio is optional. The healthyverse works in any R environment. However, RStudio provides a better development experience with:
- Enhanced autocomplete
- Integrated help
- Project management
- Visual debugging

### How much disk space does healthyverse need?

Approximately 500MB for the complete installation including all dependencies.

### Can I install healthyverse offline?

Yes, but you'll need to download all packages and dependencies on a connected system first. See [Offline Installation](Installation-Guide#offline-installation) for details.

---

## Package Management

### How do I check which version I have installed?

```r
library(healthyverse)
healthyverse_sitrep()
```

This shows versions of all healthyverse packages and R itself.

### How do I update healthyverse packages?

```r
# Check for updates
healthyverse_update()

# Then follow the printed instructions
```

Or update all packages:
```r
update.packages(oldPkgs = healthyverse_packages())
```

### Can I use only some healthyverse packages?

Yes! You can install and load individual packages:

```r
install.packages("healthyR")
library(healthyR)
```

The meta-package is just a convenience for loading all packages at once.

### What if I only need healthcare analysis, not ML?

Install only the packages you need:

```r
install.packages(c("healthyR", "healthyR.data", "healthyR.ts"))
```

### How do I uninstall healthyverse?

```r
# Remove meta-package
remove.packages("healthyverse")

# Optionally remove core packages
remove.packages(c("healthyR", "healthyR.data", "healthyR.ts", 
                  "healthyR.ai", "TidyDensity", "tidyAML", "RandomWalker"))
```

---

## Conflicts & Errors

### What does "masks" mean in the conflicts message?

When two packages have functions with the same name, the most recently loaded package "masks" (hides) the other. For example:

```
✖ tidyAML::check_duplicate_rows() masks TidyDensity::check_duplicate_rows()
```

This means `check_duplicate_rows()` will use the tidyAML version by default.

### How do I use the masked function?

Use explicit package references:

```r
# Use TidyDensity version
TidyDensity::check_duplicate_rows(data)

# Use tidyAML version
tidyAML::check_duplicate_rows(data)
```

### Can I prevent conflicts?

You can use the `conflicted` package:

```r
library(conflicted)
conflict_prefer("check_duplicate_rows", "tidyAML")
library(healthyverse)
```

### Why do I get "package not found" errors?

Common causes:
1. Package not installed: `install.packages("healthyverse")`
2. Wrong package name: Check spelling
3. Package removed: Reinstall it
4. Library path issues: Check `.libPaths()`

### Why does loading take so long?

Loading all seven packages and their dependencies takes time. To speed up:

1. Load only needed packages:
   ```r
   library(healthyR)
   library(healthyR.ts)
   ```

2. Suppress startup messages:
   ```r
   options(healthyverse.quiet = TRUE)
   library(healthyverse)
   ```

3. Upgrade to a faster system or SSD

---

## Usage & Features

### Can I use healthyverse with tidyverse?

Yes! They work great together:

```r
library(tidyverse)
library(healthyverse)
```

Both follow similar design principles and are compatible.

### What's the difference between `%>%` and `|>`?

- `%>%`: magrittr pipe, works in all R versions
- `|>`: native pipe, requires R ≥ 4.1.0

Both do the same thing:

```r
# magrittr pipe
data %>% filter(x > 5) %>% summarise(mean = mean(y))

# Native pipe
data |> filter(x > 5) |> summarise(mean = mean(y))
```

The native pipe is slightly faster but has fewer features.

### Can I use healthyverse for non-healthcare data?

Yes! While optimized for healthcare, the packages work with any data:
- TidyDensity: Statistical distributions
- RandomWalker: Random walk simulations
- tidyAML: General machine learning

### How do I cite healthyverse in publications?

```r
citation("healthyverse")
```

This provides the proper citation format for academic work.

### Are there any vignettes or tutorials?

Yes! View available vignettes:

```r
browseVignettes("healthyverse")
```

Also check the [Quick Start Tutorial](Quick-Start-Tutorial) in this wiki.

---

## Performance

### Why is my analysis slow?

Common causes and solutions:

1. **Large datasets**: Use data.table or database connections
2. **Inefficient code**: Profile with `profvis::profvis()`
3. **Memory issues**: Monitor with `pryr::mem_used()`
4. **Too many models**: Use parallel processing

### How can I speed up machine learning?

```r
# Use parallel processing with tidyAML
library(doParallel)
cl <- makeCluster(detectCores() - 1)
registerDoParallel(cl)

# Your tidyAML code here

stopCluster(cl)
```

### Can healthyverse handle big data?

Yes, but you may need to:
- Use `data.table` for large data frames
- Sample data for initial exploration
- Use database connections instead of loading all data
- Process data in chunks
- Use cloud computing for very large datasets

### How much memory do I need?

Depends on your data size:
- Small datasets (< 1GB): 4GB RAM sufficient
- Medium datasets (1-10GB): 8-16GB RAM recommended
- Large datasets (> 10GB): 32GB+ RAM or use databases

---

## Contributing

### How can I contribute to healthyverse?

Several ways:
1. Report bugs on [GitHub Issues](https://github.com/spsanderson/healthyverse/issues)
2. Suggest features via GitHub Issues
3. Improve documentation with pull requests
4. Share examples and tutorials
5. Help answer questions in Discussions

See the [Contributing Guide](Contributing-Guide) for details.

### I found a bug. What should I do?

1. Check if it's already reported in [Issues](https://github.com/spsanderson/healthyverse/issues)
2. Run `healthyverse_sitrep()` to get diagnostic info
3. Create a minimal reproducible example
4. Submit a new issue with:
   - The bug description
   - Reproducible example
   - Your `healthyverse_sitrep()` output
   - Expected vs actual behavior

### Can I request new features?

Yes! Submit feature requests on [GitHub Issues](https://github.com/spsanderson/healthyverse/issues) with:
- Clear description of the feature
- Use case and benefits
- Example of how it would work
- Any relevant references

### How do I submit a pull request?

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Update documentation
6. Submit pull request

See [Development Setup](Development-Setup) for details.

---

## Compatibility

### Does healthyverse work on Windows/Mac/Linux?

Yes! It works on all major operating systems where R runs.

### Can I use healthyverse with Shiny?

Yes! All packages work in Shiny applications:

```r
library(shiny)
library(healthyverse)

# Your Shiny app code
```

### Is healthyverse compatible with renv?

Yes! Use renv for project dependency management:

```r
# Initialize renv
renv::init()

# Install healthyverse
install.packages("healthyverse")

# Snapshot dependencies
renv::snapshot()
```

### Can I use healthyverse in R Markdown?

Absolutely! It works great in R Markdown documents:

````markdown
```{r setup}
library(healthyverse)
```

```{r analysis}
# Your analysis code
```
````

### Does it work with Quarto?

Yes! Quarto is fully supported:

````markdown
```{r}
#| label: setup
library(healthyverse)
```
````

---

## Troubleshooting

### Where can I get help?

1. **This Wiki**: Check [Troubleshooting](Troubleshooting) page
2. **GitHub Discussions**: Ask questions in [Discussions](https://github.com/spsanderson/healthyverse/discussions)
3. **GitHub Issues**: Report bugs and request features
4. **Email**: Contact maintainer at spsanderson@gmail.com
5. **Documentation**: Run `?function_name` in R

### How do I report a problem?

Include in your report:
1. Output from `healthyverse_sitrep()`
2. Minimal reproducible example
3. Expected vs actual behavior
4. Error messages (complete text)
5. Steps you've already tried

### What information should I include when asking for help?

Always include:
```r
# System information
healthyverse_sitrep()

# Session information
sessionInfo()

# Reproducible example
# Your code here
```

---

## Learning Resources

### Where can I learn more?

- **This Wiki**: Comprehensive guides and tutorials
- **Package Websites**: https://www.spsanderson.com/{package}/
- **Vignettes**: `browseVignettes("healthyverse")`
- **Examples**: `?function_name` for function examples
- **GitHub**: Example scripts and issues

### Are there any courses or workshops?

Check the package website and GitHub for:
- Workshop materials
- Tutorial videos
- Conference presentations
- Blog posts

### How can I stay updated?

- Watch the GitHub repository
- Check [Release Notes](Release-Notes)
- Follow the maintainer on social media
- Subscribe to GitHub Discussions

---

## Still Have Questions?

If your question isn't answered here:

1. Search the [GitHub Issues](https://github.com/spsanderson/healthyverse/issues)
2. Check [Troubleshooting](Troubleshooting) page
3. Ask in [GitHub Discussions](https://github.com/spsanderson/healthyverse/discussions)
4. Review package documentation: `?healthyverse`

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
