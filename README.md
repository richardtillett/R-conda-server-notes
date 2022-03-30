# Installing newer versions of R on the servers, and R packages

## The state of play

Log on to the head node val and see what R is installed.

```which R```

The output means there isn't one.

Log on to one of the nodes

```
which R
R --version
```

Try a different node. Notice any difference?


## Installation methods (a forgone conclusion)

We want to install our own R, for our own user. How should we do it?

- from source?
- using conda?

From source would be ideal. But right now, the server puts barriers in our way from installing from source. I can show those after the main demo. So instead, we will use conda to do so. Conda + R is a breeze, for installing base R, but possibly counter-intuitively, comes with potential pitfalls when it comes to installing specific packages. 

### A few notes on packages



### Installing R with conda

I want to use the latest version of R. In conda-land, the typical `R` recipe we use goes by the name `r-base`. I also like to use the `conda-forge` channel for my R installs, rather than channel `r`, and I'll show you why.

```
conda search r-base
```

To make sure we install the latest version we use the `=4.1.3` syntax. Let's build a brand new environment, give it a name, and tell it to install this exact R version from conda-forge.

```conda create -n r413 -c conda-forge r-base=4.1.3```

Use `conda activate r413` to make it active and

```
which R
R --version
```

### Installing R packages the conda way

I'll cover more below, but whenever possible, you will want to avoid using the `install.packages()` "inside-of-R" methods that you are used to. And for Bioconductor, you will even moreso want to use Conda-flavored installation methods instead of the ones found on bioconductor's site.



And keep paying attention to the version numbers. These will help you if something goes wrong. Since we're installing things with a lot of dependencies, and in a complicated way, the odds are that something eventually will. **Think of this like a puzzle. And a problem to be solved.**

The first thing I do for any R install is get `tidyverse` tools. What is tidyverse? it includes:

* dplyr
* ggplot2
* readr
* tidyr
* and more [www.tidyverse.org](https://www.tidyverse.org)

you *could* install it the normal way, but you ***shouldn't***

use a conda-centric method to install it
google for the phrase *tidyverse conda-forge*

```
conda install -c conda-forge r-tidyverse=1.3.1
```

# dont do this

R

BiocManager::install("DESeq2")

do this

```
conda install -c bioconda bioconductor-deseq2
```

If it is popular on bioconductor, odds are it has a bioconda bioconductor recipe

### Misc Advice

Above I hinted at advice not to install using normal methods. The re are a couple of related reasons
1) any tool you install inside of R, conda will have no idea about and could break the dependencies of that tool the next time you install something the conda way.
2) The way conda protects the exact libraries and versions from getting broken ends up acting orthogonally to the work `BiocManager` and `install.packages` try to do. They try to modify or update some library that was put there by conda, aren't allowed to, then fail-over into just putting these dependencies **somewhere they can write to.** This causes bloat, but even worse from the user's perspective, makes installation take FOREVER. 

To see what would blow up by installing it the BiocManager way, I tried installing DESeq2 and gave up after half an hour of it installing thousands of bespoke dependencies over and over again. I assume it might eventually install, but can't say for sure.

On the other hand, simpler packages, and with the simpler `install.packages()` installer do actually *eventually* install something, and the installed packages do work. So you can use the native methods as a last resort. Just, expect things to eventually break the more you step outside of the conda world.
