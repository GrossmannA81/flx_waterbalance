---
editor_options: 
  markdown: 
    wrap: 72
---

# The GECO R project template

Hoi.

This is a github template for a GECO R project repository. This template
can be loaded when creating a new project in github. As such you
pre-populate a project with a standard set of directories. This ensures
a project-oriented and consistent workflow across all lab members, and
removes some of the mental overhead in making decisions on a project
structure.

It must be noted that some of the functionality as provided by this
setup is included in the [`usethis`](https://usethis.r-lib.org/) R
package, the aim is slightly different. For one, `usethis` is aimed at
populating a package structure. Second, there is not preset full
template, hence many individual decisions still need to be made and code
executed. This is bypassed by this setup.

## Use

### A github project from the template

To use the template create a new repository on github, as you otherwise
would using the big green button. If you are in the project on github
you can hit the green button top right (Use this template).

![](https://github.com/bluegreen-labs/environmental_data_science_101/raw/main/images/green_button.png)

Otherwise you can select the repository from the template dropdown menu,
select `geco-bern/R-project-template`.

![](https://github.com/bluegreen-labs/environmental_data_science_101/raw/main/images/new_repo_1.png)

Proceed as usual by naming your repository. However, be careful to
select the correct owner of the project if you have multiple identities.
![](https://github.com/bluegreen-labs/environmental_data_science_101/raw/main/images/new_repo_2.png)

### Clone to your local computer

The new repository will be created and populated with the files of the
template. You can then clone the project to your local computer to add
files.

Although optional it is wise to rename the `*.Proj` file as this will
not automatically change to your repository name. However, retaining the
original name should not change any of the functionality.

``` bash
git clone git@github.com:USER/YOUR_PROJECT.git
```

## Structure

The structure of the template follows the structure of an R package
without actually being one. There are several reasons for this.

-   Familiarizes you with an R package structure
    -   allowing for an optional switch to an R package
-   Avoids top level aggregation of data, code and reporting files
-   Splits the dynamic reporting from academic writing (`vignettes` vs.
    `manuscript`)
-   Splits pre-processing of data from working / included data
    (`data-raw` vs. `data`)
-   Splits R code from other scripts (bash / python in `src`)
-   Splits R functions from R analysis scripts (`R` vs `analysis`)

Below you find a comprehensive list of what goes where an why, as well
as some best practices on how to structure further data within these
folders.

### The R folder

The `R` folder contains R functions, not scripts. This means code
wrapped in a structure as such

``` r
# A demo function
#
# This function demonstrates the general layout
# of a function

my_function <- function(parameter) {
  some_actions
}
```

Functions are actions you need more than once, which can not be
generated easily with external packages and are tailored to your
project.

These functions should stand on their own with limited links to
additional custom functions. Ideally you provide a brief title and
description on the function's purpose before.

Writing functions seems an initial waste of time, you could easily just
copy and paste some code in your analysis scripts. However, this means
that if you decide certain aspects of this workflow you might have to
hunt down these changes in all analysis scripts. Failing to do so will
result in corrupted analysis. In addition, writing functions will make
it easy to re-use the code within the context of a new project, and if
proven to be generally useful outside a single research project it can
be integrated in a formal package.

### The src folder

The `src` folder contains scripts and code which is not R related, in
packages this folder often contains Fortran or C code which needs to be
compiled. Here, it is common to store bash or python functions which
might assist in data cleaning or data gathering which can't be done in R
alone.

### The data-raw folder

The `data-raw` folder contains, as the name suggests, raw data and the
scripts to download and pre-process the data. This is data which
requires significant pre-processing to be of use in analysis. In other
words, this data is not analysis ready (within the context of the
project).

To create full transparency in terms of the source of this raw data it
is best to include (numbered) scripts to download and pre-process the
data. Either in these scripts, or in a separate README, include the
source of the data (reference) Ultimately, the output of the workflow in
data-raw is data which is analysis ready.

It is best practice to store various raw data products in their own
sub-folder, with data downloading and processing scripts in the main
`data-raw` folder.

```         
data-raw/
├─ raw_data_product/
├─ 00_download_raw_data.R
├─ 01_process_raw_data.R
```

Where possible it is good practice to store output data (in `data`)
either as human readable CSV files, or as R serialized files (generated
using with the `saveRDS()` function).

It is common that raw data is large in size, which limits the option of
storing the data in a git repository. If this isn't possible this data
can be excluded from the git repository by explicitly adding directories
to `.gitignore` to avoid accidentally adding them.

When dealing with heterogeneous systems dynamic paths can be set to
(soft) link to raw-data outside the project directory.

### The data folder

The `data` folder contains analysis ready data. This is data which you
can use, as is. This often contains the output of a `data-raw`
pre-processing workflow, but can also include data which doesn't require
any intervention, e.g. a land cover map which is used as-is. Output from
`data-raw` often undergoes a dramatic dimensionality reduction and will
often fit github file size limits. In some cases however some data
products will still be too large, it is recommended to use similar
practices as describe for `data-raw` to ensure transparency on the
sourcing of this data (and reproducible acquisition).

It is best to store data in transparently named sub-folders according to
the product type, once more including references to the source of the
data where possible. Once more, download scripts can be used to ensure
this transparency as well.

```         
data/
├─ data_product/
├─ 00_download_data.R
```

### The analysis folder

The `analysis` folder contains, *surprise*, R scripts covering analysis
of your analysis ready data (in the `data` folder). These are R scripts
with output which is limited to numbers, tables and figures. It should
not include R markdown code!

It is often helpful to create additional sub-folders for statistics and
figures, especially if figures are large and complex (i.e.
visualizations, rather than graphical representations of statistical
properties, such as maps).

Scripts can have a numbered prefix to indicate an order of execution,
but this is generally less important as you will work on analysis ready
data. If there is carry over between analysis, either merge the two
files or use numbered prefixes.

```         
analysis/
├─ statistics/
│  ├─ 00_random_forest_model.R
│  ├─ 01_random_forest_tuning.R
├─ figures/
│  ├─ global_model_results_map.R
│  ├─ complex_process_visualization.R
```

Output of the analysis routines can be written to file (`manuscript`
folder) or visualized on the console or plot viewer panel.

### The manuscript folder

The `manuscript` folder contains a true working document often written
in an external word processing software. It also, at times, contain the
output of any analysis script, such as tables and rendered figures.

Thee can be an R markdown file if for example suitable templates can be
found in the [`rticles`](https://pkgs.rstudio.com/rticles/) R package to
facilitate publication. However, the use of R markdown should be done
with much care (see notes on the `vignettes` folder). As before, use
sub-folders to organize this work neatly.

### The vignettes folder

The `vignettes` folder contains dynamic notebooks, i.e. R markdown
files. These might serve a dual use between analysis and manuscript.
However, the use case in reality should be considered very narrowly. In
general, as they are commonly used, R markdown files are rarely
portable. The argument that it is easy to share rendered html files is
invalid if you adhere to an open workflow with github based snapshots.
The latter ensures that all code is visible, all data is visible, and
the project is truly reproducible. Furthermore, R markdown documents mix
two cognitive tasks, writing text and writing code. Switching between
these two modes comes with undue overhead. If you code, you should not
be writing prose, and vise versa.

Unless applied to small, educational, examples a markdown file has
little place in a code heavy environment. In short, if your R markdown
file contains more code than it does text, it should be considered an R
script or function (with comments or documentation). Conversely, if your
markdown file contains more text than code it probably is easier to
collaborate on a true word processing file (or a Google Docs file). The
use case where the notebooks might serve some importance is true
reporting of general statistics.

Finally, the use of R markdown also encourages bad project management
practices. Most commonly this originates from the fact that rendering of
the document is relative to the location of the document itself. If no
session management tools such as the package
[`here`](https://here.r-lib.org/) are used this automatically causes
files to pile up in the top most level of a project, undoing most
efforts to structure data and code. This is further compounded by the
fact that there is a tendency to remain within the working environment
(document), and therefore code blocks which should be functions are not
translated as such.

In short, R markdown files have their function in reporting results,
once generated (through functions or analysis scripts) but should be
avoided to develop code / ideas (see cognitive switching remark)!

### Capturing your session state

If you want to ensure full reproducibility you will need to capture the
state of the system and libraries with which you ran the original
analysis. Note that you will have to execute all code and required
libraries for `renv` to correctly capture all used libraries.

When setting up your project you can run:

``` r
# Initiate a {renv} environment
renv::init()
```

To initiate your static R environment. Whenever you want to save the
state of your project (and its packages) you can call:

``` r
# Save the current state of the environment / project
renv::snapshot()
```

To save any changes made to your environment. All data will be saved in
a project description file called a lock file (i.e. `renv.lock`). It is
advised to update the state of your project regularly, and in particular
before closing a project.

When you move your project to a new system, or share a project on github
with collaborators, you can revert to the original state of the analysis
by calling:

``` r
# On a new system, or when inheriting a project
# from a collaborator you can use a lock file
# to restore the session/project state using
renv::restore()
```

> NOTE: As mentioned in the {renv} documentation: "For development and
> collaboration, the `.Rprofile`, `renv.lock` and `renv/activate.R`
> files should be committed to your version control system. But the
> `renv/library` directory should normally be ignored. Note that
> `renv::init()` will attempt to write the requisite ignore statements
> to the project `.gitignore`." We refer to \@ref(learning-objectives-6)
> for details on github and its use.
