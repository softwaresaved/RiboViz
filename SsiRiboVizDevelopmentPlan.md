# RiboViz Proposed Development Plan

Mike Jackson, The Software Sustainability Institute based on plan by Eilidh Grant, EPCC.

28 August 2018

## Introduction

This document describes proposed workplan for RiboViz development, based on Eilidh's original plan. It is formed of three phases: preparing RiboViz for refactoring, refactoring and the development of new functionality. The intention is that the refactoring of RiboViz delivers a maintainable, usable suite of software which will then serve as a sound basis for the development of new functionality and, hopefully, enable RiboViz to contribute to novel research.

## Prepare RiboViz

### Extend documentation

* Extend documentation to cover:
  - How to deploy RiboViz on target platforms.
  - How to run RiboViz command-line tools.
  - How run the web site and Shiny server locally.
* These can be provided as a side-effect of a developer's familiarisation with RiboViz.
* Benefits:
  - Improves ease with which others can use RiboViz.
  - Increases RiboViz's [bus factor](https://en.wikipedia.org/wiki/Bus_factor) by documenting knowledge in RiboViz's developers' heads.
* Dependencies:
  - Software knowledge from RiboViz developers.
* Effort: 1 week

### Create RiboViz organisation on GitHub

* Benefits:
  - Convey impression that RiboViz is software that is developed by a project or community rather than a solo developer.
  - Help contribute to building a user and developer community in the longer-term as RiboViz will be in a more neutral space than a personal, or institutional-specific, repository.
* Effort: 1 day

### Implement regression test suite

* Implement suite of regression tests for current command-line tools, beyond just the current "vignette".
* Benefits:
  - Provides a test oracle against which refactored components can be tested, and can help to identify any bugs introduced during refactoring.
* Dependencies:
  - Domain knowledge from RiboViz developers.
* Effort: 2 week

### Fix summary bug

* Summaries produced by R scripts used to look as expected (a strong peak at -12, and peaks every 3 bases after start), but no longer do so.
* Track down bug by producing fake data in .h5 format, producing summary statistics for ths, viewing statistics to see if peaks are where expected.
* Implement fix for bug.
* Update regression test oracle in light of fix to help identify if bug reoccurs.
* Benefits:
  - Makes the RiboViz workflow scientifically useful.
* Dependencies:
  - Implement regression test suite.
  - Software and domain knowledge from RiboViz developers.
* Effort: 1 week (may need revising depending on nature of fix required)

## Refactor RiboViz

### Create Python package(s)

* Refactor current Python scripts so that any functionality is wrapped into functions in separate Python/R scripts from those that handle command-line invocation. 
* Evolve a suite of tests for the functions in a Python-compliant test framework (e.g. pytest). Consider in-code auto-generation of dummy/sample data for tests.
* Create Python package(s) with command-line tools and Python modules.
* Create a Git repository within the RiboViz organisation on GitHub for each package.
* Benefits:
  - Decouples user interfaces from underlying functionality.
  - Makes it easier to test discrete parts of the functionality in isolation
  - Allows functionality to be used within other applications as libraries.
  - Monolithic RiboViz Git repository becomes a suite of cohesive components.
* Dependencies:
  - Create RiboViz organisation on GitHub.
  - Implement regression test suite.
  - Domain knowledge from RiboViz developers (for writing tests).
  - Can be done concurrently with Create R package(s) below.
* Effort: 3 week

### Create R package(s)

* As above, but for R and using an R-compliant test framework (e.g. testthat).
* Effort: 3 week

### Create workflow package

* Reimplement bash workflow script (prepRiboviz.sh) in Python.
* Ensure key parameters to control commands invoked by the script can be configured via a configuration file.
* Document how to install Python packages and R packages as prerequisites.
* Create a Git repository within the RiboViz organisation on GitHub for the workflow package.
* Benefits:
  - Makes workflow script easier to maintain and test.
  - Removes need for non-programmers to need to edit code, unless they want to.
  - Monolithic RiboViz Git repository becomes a suite of cohesive components.
* Dependencies:
  - Create RiboViz organisation on GitHub.
  - Implement regression test suite.
  - Create Python package(s).
  - Create R package(s).
  - Domain knowledge from RiboViz developers (for writing tests).
* Effort: 2 week
* **Note: this may depend on whether Common Workflow Language (CWL) is to be adopted. If it, or another bioinformatics engine, is to be used, then increase the effort to 4 week.

### Create Shiny/web pages package

* Relocate Shiny/web pages components into their own Git repository.
* Document how to install Python package(s) and R package(s) as prerequisites.
* Benefits:
  - Monolithic RiboViz Git repository becomes a suite of cohesive components.
* Dependencies:
  - Create RiboViz organisation on GitHub.
  - Create Python package(s).
  - Create R package(s).
* Effort: 2 week

### Implement integration tests suite

* Implement integration tests suite for workflow package.
* Benefits:
  - Increase coverage of tests applied to RiboViz.
  - Help detect issues that tests of individual packages do not identify.
* Dependencies:
  - Create a workflow package.
  - Domain knowledge from RiboViz developers.
* Effort: 2 week

### Update data representation.

* Refactor how data is represented in .h5 files
* Currently, there are two kinds of data:
  - General yeast/species info e.g. tRNA frequency etc.
  - Transcriptomes to align to, which specifies the start of the gene.
* h5 data has read data, matrix of start position and length and count.
* Dependencies:
  - Domain knowledge from RiboViz developers.
* Effort: 2 week

### Implement Travis CI test jobs

* Dependencies and mix of languages may pose challenges. However, refactoring tasks above may make this easier as Python-only and R-only components could be tested in isolation.
* If a job takes too long to configure its dependencies, then Travis CI's non-configurable timeouts will trigger if either no output is printed for 10 minutes or a job takes longer than 50 minutes to run.
* Benefits:
  - Run tests automatically on push of updates to GitHub.
  - Ensures tests are run regularly.
* Dependencies:
  - Can be done concurrently with Create Python package(s) and Create R package(s).
* Effort: 1 week

## Extend RiboViz

### Implement script to get transcriptome from annotated genome

* Check genome has start and end codons and some RNA ether side.
* Process transcriptome to get species-specific data
* Edward Wallace already has a script.
* Dependencies:
  - Domain knowledge from RiboViz developers.
* Effort: 2 week

### Refactor analysis scripts to work with other data sets

* Currently, RiboViz handles yeast datasets.
* A quick win is to refactor analysis scripts to ensure that statistics do not depend on data that may only be available for yeast
* Dependencies:
  - Implement script to get transcriptome from annotated genome
  - Domain knowledge from RiboViz developers.
* Effort: 2 week

### Implement summary statistics and plots

* scripts/generate_stats_figs.R processes .h5 files, extracts summary statistics and creates plots.
* Use this the basis of a new Shiny scripts that can process new data, generate statistics, output summary tables, and plot results.
* Wrap plots in a Shiny script.
* Dependencies:
  - Domain knowledge from RiboViz developers.
* Effort: 2 week

### Refactor Shiny/web pages to work with other data sets

* Refactor Shiny server so it can be customised to work with other data sets.
* Ideally, adopt a modular, pluggable design.
* Dependencies:
  - Domain knowledge from RiboViz developers.
* Effort: 3 week

### Automate adding a new data set to Shiny/web pages

* Develop a script to automate addition of a new data set to the web site and Shiny server.
* Dependencies:
  - Refactor Shiny/web pages to work with other data sets.
  - Domain knowledge from RiboViz developers.
* Effort: 3 week

## Summary

The total effort for each phase, and the plan as a whole, is:

|  Wks | Task |
| ---- | ---- |
|      | Prepare RiboViz |
|      | |
|  1   | Extend documentation |
|  0.2 | Create RiboViz organisation on GitHub |
|  2   | Implement regression test suite |
|  1   | Fix summary bug |
|      | |
|      | Refactor RiboViz |
|      | |
|  3   | Create Python package(s) |
|  3   | Create R package(s) |
|  2   | Create workflow package |
|  2   | Create Shiny/web pages package |
|  2   | Implement integration tests suite |
|  2   | Update data representation. |
|  1   | Implement Travis CI test jobs |
|      | |
|      | Extend RiboViz |
|      | |
|  2   | Implement script to get transcriptome from annotated genome |
|  2   | Refactor analysis scripts to work with other data sets |
|  2   | Implement summary statistics and plots |
|  3   | Refactor Shiny/web pages to work with other data sets |
|  3   | Automate adding a new data set to Shiny/web pages |
| 31.2 | **Total** |

Note that while I have endeavoured to be liberal in my estimates, the developer(s) assigned to carry out these tasks may revise the estimates in light of their own skills and experience. This especially applies to estimates for Extend RiboViz to support new scientific research tasks which rely more heavily on access to domain-specific knowledge.
