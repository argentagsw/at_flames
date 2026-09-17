# Setting up FLAMES with conda

[← Back to the main README](../README.md)

This page describes how to prepare an R/Python environment for `at_pipeline_part1_v2` using conda.
The environment installs into your own conda prefix, so **no sudo or administrator access is required**. 

> [!CAUTION]
> The FLAMES authors recommend running FLAMES from their Docker/Singularity images rather than
> from conda (see the [FLAMES README](https://github.com/mritchielab/FLAMES/)). That advice
> concerns compiling R packages from source inside conda. The procedure below avoids that by
> installing the pre-built bioconda package, and in our tests it gave the same results as the
> container. If containers are available on your system, they should be the recommended option.

## Version requirements

| Component | Version |
|---|---|
| FLAMES | **≥ 2.3.3**, recommended **2.4.2** (the version published on bioconda) |
| R | ≥ 4.5 (the bioconda build of FLAMES 2.4.2 uses R 4.5) |
| Python | ≥ 3.8 |

> [!IMPORTANT]
> FLAMES 2.3.3 is the first version that accepts a pre-built minimap2 index (the `.mmi` file
> passed with `--genome_mmi`). The pipeline relies on it, so older versions fail at step 7.

## 1. Create the environment with FLAMES pinned

```bash
conda create -n flames_at -y -c conda-forge -c bioconda "bioconductor-flames=2.4.2"
```

> [!WARNING]
> Always pin the version. bioconda still publishes the old 1.x line of FLAMES, so an unpinned
> `conda install bioconductor-flames` may install a version that is too old for this pipeline.

## 2. Add the remaining packages

```bash
conda activate flames_at

conda install -y -c conda-forge -c bioconda --freeze-installed \
      bioconductor-dropletutils r-reticulate r-svglite r-ggplot2 r-scales \
      r-optparse r-jsonlite matplotlib-base
```

> [!TIP]
> `--freeze-installed` keeps the solver from downgrading FLAMES.

## 3. Check the environment

```bash
Rscript -e 'library(FLAMES); packageVersion("FLAMES")'   # must be 2.3.3 or newer
```

## 4. Run the pipeline

Activate the environment before launching the pipeline, since the binary calls `Rscript` and
`python3` from the `PATH`:

```bash
conda activate flames_at
./at_pipeline_part1_v2 [arguments]
```

> [!NOTE]
> On an HPC cluster, activate the environment inside the job script as well. Keep in mind that
> step 3 (Elbow Analysis) is interactive, so run steps 1–3 in an interactive session and submit the
> rest with `--skip_steps 1,2,3`.
