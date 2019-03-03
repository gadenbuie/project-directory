A best-practices workflow for setting up a new data science project directory in the terminal or R
==================================================================================================

------------------------------------------------------------------------

Basic workflow of data analysis
-------------------------------

------------------------------------------------------------------------

Not every data analysis is the same and but this is a useful template
for what the pieces of a data analysis are and how they flow together.

-   Define question
-   Determine what data you can access
-   Obtaining the data
-   Cleaning the data
-   Exploratory data analysis
-   Statistical prediction/modeling
-   Interpretation of results
-   Challenging of results
-   Synthsis and write up
-   Creating reproducible code

Creating a project skeleton
---------------------------

------------------------------------------------------------------------

### Linux

------------------------------------------------------------------------

Create a project directory and nested subdirectories with a one-liner in
the `Terminal`:

``` bash
mkdir -p hsapiens-snps/{data/{raw,tidy},refs,src,bin,analysis,figures}  # no spaces between subdirectories
```

-   All of your raw data goes into `raw` \# Compress (`gzip file.fasta`)
    and read-only (`chmod 400 file.fasta`)
-   All of your post processed data goes into `tidy`
-   Reference Data (*e.g.* Genomes/Transcriptomes) go into `refs`
-   Source code for downloaded tools go into `src`
-   Scripts you’ve written and compiled binaries go into `bin`
-   All of the intermediary files generated, should go into `analysis`
    folder.
    -   You can then logically seperate subprojects
        (e.g. `sequencing-data-qc`, `alignment-results-qc`,
        `diff-exp-analysis`, etc.)

### R

------------------------------------------------------------------------

There are a few differences between standard skeletons on terminal and
`R`.

-   A `src` folder is not required for `R` projects
-   Scripts should be kept in the `R` folder and you should have one
    script to run eveything if possible in the main directory

Create `New Project` with `RStudio` (*e.g.* hsapiens-snps) and then
create the subfolders

``` r
folder_names <- c("data/raw", "data/tidy", "refs", "R", "analysis", "figures")
sapply(folder_names, dir.create)
```

In `R` use the `file.create()` function. To verify that the file has
been created use `list.files()`. Use `file.rename()` function to change
names or the `file.remove()` to remove files, and `file.exists()` to
check if a file exists. You can use the `dir.create()` function to
create directories in `R` as well.

If you project is only based in `R` you should create one folder per
project with the files organized like this:

``` r
name_of_project
|--data
    |--raw
        |--file_001.xlsx
        |--file_002.gen
        |--file_002.sample
    |--tidy
        |--file_001-cleaned.csv
|--refs
    |--Oldach_2018.pdf
|--analysis
    |--01-analysis.Rmd
|--figures
    |--01-scatterplot.jpg
    |--01-correlation.png
|--R
    |--exploratory_analysis.R
    |--pdf_scraper.R
|--name_of_project.Rproj
|--run_all.R
```

File naming
-----------

------------------------------------------------------------------------

-   Avoid spaces: `2018 eda report.md` is not a good name but
    `2018_eda_report.md` is.
-   Avoid punctuation, periods and other special characters except for
    underscores and dashes.
-   Always use lower case: `Esophogael-Cancer_Report.md` is not a good
    name but `esophogael-cancer_report.md` is.
-   Use leading zeros (left-pad) for correct ordering of files with
    `ls`: Use `file-021.txt` **NOT** `file21.txt`.
    -   The numbers are determined by how many files you will
        potentially have. So if you may not have more than 1000 files
        you can choose three digits. If not more than a hundred files
        choose two digits and so on.

Create dated directories/files using the command date `+%F` for ISO 8601
(`yyyy-mm-dd`). If you keep a digital notebook you can `Ctrl+F` to look
for something see what date you did it and easily find that
subdirectory.

``` bash
mkdir results-$(date +%F)
```

The `README` file
-----------------

------------------------------------------------------------------------

Place a `README` file in each directory explaining how code is organized
(*e.g.* `data/README.txt` contains metadata about all data files in data
directory). For each filename, I recommend to have short description of
what the file is for. Document your methods/workflow, definition of code
and symbols used to deal with missing data, include URL’s for references
to publication or external data and document when you download data
(database release number/version number/names, etc.) and if you used
MySQL or UCSC Genome browser to download data.

Update the `README` file as you work. As you create new files and
folders, add their descriptions to the `README`. Include a list of
variables, units of measurement of each variable, and information about
how others should cite your analysis.

Creating a research commpendium
-------------------------------

------------------------------------------------------------------------

Temple Lang and Gentleman (2007) advance the proposal for using the R
package structure as a “Research Compendium,” an idea that has since
caught on with many others.

-   [Papers with publicly available datasets may recieve a higher number
    of citations than similar studies without available
    data](https://doi.org/10.1080/00031305.2017.1375986)
-   Data sharing is associated with higher publication productivity
-   A commpendium makes it easier to communicate our work with others,
    including collaborators and (not least of all) our future selves

It may be tempting to organize all scripts and reports with ascending
names, for example `001-load.R`, `002-clean.R` *etc.* and although it
helps with organization it does not capture the full tree of
dependencies. In the terminal it would be best to use a `Makefile` to
manage more complex workflows as they capture the full tree of
dependencies and control the order of code execution.

On `R` I would strongly recommend using the [Drake
package](https://github.com/ropensci/drake) which I’ve talked about
recently: [“Getting Started with Drake in
R”](https://moldach.github.io/xaringan-presentation_drake/#1).

If your workflow combines `R` code, `terminal` and `python` I would
suggest using [`snakemake`](https://snakemake.readthedocs.io/en/stable/)
or
[`Airflow`](https://towardsdatascience.com/getting-started-with-apache-airflow-df1aa77d7b1b).
(*TO DO*: look into the `reticulate` package and see how complex
multi-language workflows are managed)

### Automatically create project skeletons in R

------------------------------------------------------------------------

The [`lockedata/starters`
package](https://github.com/lockedata/starters) on Github provides a
number of functions, leveraging the `usethis` pacakge, designed to take
away *some* of the grunt work involved in setting up new projects. The
`createAnalysisProject()` function creates a project ready for a typical
analysis project with `packrat` to manage dependencies to keep things
consistent and reproducible, while the `createPackageProject()` helps
setup a project with code coverage, vignettes, unit testing *etc.* out
of the box.

As a project grows in size and collaboration, having the more rigid
structure offered by a package format becomes increasingly valuable.
Packages can automate installation of dependencies, perform checks that
changes have not broken code, and provide a modular and highly tested
framework for collecting together the three essential elements: data,
code, and manuscript-quality documentation, in a powerful and
feature-rich environment.

Packages are meant to be distributed, so viewing a project as a package
means preparing your project to be distributed. This can help keep you
honest when programming; you’re less likely to take shortcuts when you
plan on releasing it to the world. Furthermore, others can start using
the tools you made and that can earn you citations or even an additional
paper in [*J. Stat. Soft.*](https://www.jstatsoft.org/index) or the [*R
Journal*](https://journal.r-project.org/).

### Template for scholarly writing (`RMarkdown` and `bookdown`)

------------------------------------------------------------------------

Another tool I have not yet tried yet (as I’m a `ctb` to the
`lockedata/starters` package) but looks promising is
[`rrtools`](https://github.com/benmarwick/rrtools). It provides a
convienent starting point for writing a journal article, report, or
thesis.

### Publish your analysis to a website

------------------------------------------------------------------------

The [`workflowr`
package](https://cran.r-project.org/web/packages/workflowr/index.html)
combines literate programming (`knitr` and `rmarkdown`) and version
control (`Git` via `git2r`) to generate a website containing
time-stamped, versioned, and documented results.

Acknowledgments
---------------

------------------------------------------------------------------------

-   [A Quick Guide to Organizing Computational Biology
    Projects](http://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1000424)
-   [Best Practices for Scientific
    Computing](https://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.1001745)
-   [Good Enough Practices in Scientific
    Computing](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1005510)
-   [The Biostar Handbook](https://www.biostarhandbook.com/)
-   [Organizing Data Science
    Projects](https://leanpub.com/universities/courses/jhu/cbds-organizing)
-   [Designing
    projects](https://nicercode.github.io/blog/2013-04-05-projects/), by
    [Rich Fitzjohn](https://nicercode.github.io/about/#Team)
-   [How I Start A Bioinformatics
    Project](http://lab.loman.net/2014/05/14/how-i-start-a-bioinformatics-project/),
    by [Nick Lowman](http://lab.loman.net/about/)
-   [Report Writing for Data Science in
    R](https://leanpub.com/reportwriting), by [Roger D.
    Peng](http://www.biostat.jhsph.edu/~rpeng/)
-   [here, here](https://github.com/jennybc/here_here), by [Jenny
    Bryan](https://www.stat.ubc.ca/~jenny/)
-   [here documentation](https://github.com/r-lib/here), by [Kirill
    Muller](https://github.com/krlmlr)
-   [File organization best practices by Andrew
    Tran](https://andrewbtran.github.io/NICAR/2018/workflow/docs/01-workflow_intro.html?utm_content=buffer858fd&utm_medium=social&utm_source=twitter.com&utm_campaign=buffer)
-   [ropensci/rrrpkg: tools and templates for making research
    compendia](https://github.com/ropensci/rrrpkg#useful-tools-and-templates-for-making-research-compendia)
