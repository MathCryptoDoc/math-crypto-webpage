---
title: "Analysis for v0.9.26"
---

We investigated the benchmark scores on a recent CPU (Intel i7-12700) with NVMe drive for polkadot version 0.9.26. All possible variations of the optimization options explained [here]({{< ref "/optimized/optim-options" >}} "Optimization options") were tested. This resulted in 36 different *builds* (including the official polkadot binary and docker image) that are listed below:

{{< expand "Build options" "Click to expand..." >}}
{{< bootstrap-table table_class="table table-dark table-striped table-bordered" >}}
|   nb_build | arch      | toolchain   | codegen   | lto_ldd   | profile    |
|-----------:|:----------|:------------|:----------|:----------|:-----------|
|         10 | none      | stable      | False     | False     | release    |
|         11 | none      | stable      | False     | True      | release    |
|         12 | none      | stable      | True      | False     | release    |
|         13 | none      | stable      | True      | True      | release    |
|         14 | skylake   | stable      | False     | False     | release    |
|         15 | skylake   | stable      | False     | True      | release    |
|         16 | skylake   | stable      | True      | False     | release    |
|         17 | skylake   | stable      | True      | True      | release    |
|         18 | alderlake | stable      | False     | False     | release    |
|         19 | alderlake | stable      | False     | True      | release    |
|         20 | alderlake | stable      | True      | False     | release    |
|         21 | alderlake | stable      | True      | True      | release    |
|         22 | none      | nightly     | False     | False     | release    |
|         23 | none      | nightly     | False     | True      | release    |
|         24 | none      | nightly     | True      | False     | release    |
|         25 | none      | nightly     | True      | True      | release    |
|         26 | skylake   | nightly     | False     | False     | release    |
|         27 | skylake   | nightly     | False     | True      | release    |
|         28 | skylake   | nightly     | True      | False     | release    |
|         29 | skylake   | nightly     | True      | True      | release    |
|         30 | alderlake | nightly     | False     | False     | release    |
|         31 | alderlake | nightly     | False     | True      | release    |
|         32 | alderlake | nightly     | True      | False     | release    |
|         33 | alderlake | nightly     | True      | True      | release    |
|         34 | none      | stable      | False     | False     | production |
|         35 | none      | stable      | True      | False     | production |
|         36 | skylake   | stable      | False     | False     | production |
|         37 | skylake   | stable      | True      | False     | production |
|         38 | alderlake | stable      | False     | False     | production |
|         39 | alderlake | stable      | True      | False     | production |
|         40 | none      | nightly     | False     | False     | production |
|         41 | none      | nightly     | True      | False     | production |
|         42 | skylake   | nightly     | False     | False     | production |
|         43 | skylake   | nightly     | True      | False     | production |
|         44 | alderlake | nightly     | False     | False     | production |
|         45 | alderlake | nightly     | True      | False     | production |
|   official | none      | nightly     | True      | False     | release    |
|     docker | none      | nightly     | True      | False     | release    |
{{< /bootstrap-table >}}
{{< /expand >}}

For each build, we repeated the following benchmark 20 times:
```Shell
polkadot benchmark machine --disk-duration 30
```
Total CPU utilization before and after each test was negligible (< 1%) to make sure that the benchmark was not disturbed by competing CPU tasks.

{{< hint type=note >}}
Starting with polkadot version 0.9.27, there is a new benchmark for timing the execution speed of an extrinsic. We will include this score in future analyses.
{{< /hint >}}

## All comparisons

Below we plot the scores for each build in a [box plot](https://en.wikipedia.org/wiki/Box_plot). The red line indicates the median and the box starts from the first to the third quartile values of the scores. Outliers are indicated with circles.

We first compare **CPU scores**. There are clear differences visible but there is no one winning build. We will investigate good build options below.

![CPU scores](scores_cpu_i7_12700.svg)

Regarding **disk scores**, all builds behave very similarly except the docker image, which is worse for random write.

![Disk scores](scores_disk_i7_12700.svg)

For **memory score**, the situation is similar to random write (only docker underperformed).

{{< figure src="scores_mem_i7_12700.svg" width="50%" alt="Memory scores" >}}

## Preliminary conclusions

A few conclusions are easy to draw:
- The *official polkadot binary* is somewhat optimized but there is a lot of room for improvement.
- The *official docker image* is about as fast as the official polkadot binary, except for random write and copy, where you have a relative penalty of 6% and 13%, resp.
- Compiling *for a specific CPU* can improve the CPU scores by about 6%.
- Compiling *without optimization options at all* gives worse CPU scores.
- For the *disk and memory scores*, the differences were not statistically significant. In the box plots above, the median of one build is almost always included in the boxes of the other builds.


## Finding good optimization options

The optimization options had a clear influence on the CPU scores (but not for disk and memory). Let us therefore plot both CPU scores and label a few interesting builds.

![Memory scores](scatter_cpu_i7_12700.svg)

Determining a winning build is not so easy:
- There are two CPU scores. Ideally, we want to find *the best build for both scores*, but from the figure we see that this is not possible: number 45 wins in SR25519-Verify, while number 33 wins BLAKE2-256. 
- As a compromise, we look for a build that has *good scores for both CPU benchmarks*. One way is to simply add the scores but they have different scales. A general practice for dealing with more than one objective is to find solutions (here, builds) on the [Pareto front](https://en.wikipedia.org/wiki/Pareto_front): Given a build, there is no other build that is better in SR25519-Verify *and* in BLAKE2-256. For our case, such Pareto efficient builds are numbers 45, 32, and 33. They can be found in the top right corner of the figure.
- Two builds, like 39 and 45, that have very similar scores are not that different in practice. The fact that one is better than the other is probably due to chance. (This can be made more precise by estimating the error on the score which is related to the width of the box plots in the figures above. We will not do this for now.) We therefore also include builds that are very close to the Pareto efficient ones. 

In the figure, we labeled the **builds that can all be considered very good** since they are Pareto efficient or very close to it. This gives us the following winning optimization options:

{{< bootstrap-table table_class="table table-dark table-striped table-bordered" >}}
|   nb_build | arch      | toolchain   | codegen   | lto_ldd   | profile    |
|-----------:|:----------|:------------|:----------|:----------|:-----------|
|         17 | skylake   | stable      | True      | True      | release    |
|         20 | alderlake | stable      | True      | False     | release    |
|         21 | alderlake | stable      | True      | True      | release    |
|         32 | alderlake | nightly     | True      | False     | release    |
|         33 | alderlake | nightly     | True      | True      | release    |
|         39 | alderlake | stable      | True      | False     | production |
|         45 | alderlake | nightly     | True      | False     | production |
{{< /bootstrap-table >}}

We see some **clear patterns**:
- Compiling for the most specific *CPU architecture* (alderlake for our node) is clearly a good idea.
- Enabling *codegen* (that is, ``codegen=1``) is always good.
- For the *release profile*, compiling with LTO is not needed but not harmful either.  Note that the *production profile* is always compiled with LTO.
- Choosing *stable or nightly toolchain* does not seem to matter that much.

For completeness, we check again the good candidate builds. Except for one bad outlier in build number 33, there is not much more information here: *all builds have more or less the same spread*. (A build with a high median score but a large spread would not be desirable due to unpredictable performance.)

![Memory scores](pareto_scores_cpu_i7_12700.svg)


## Winning optimization options

Based on the above analysis, we recommend choosing build option 32. This corresponds to
```Shell
rustup override set nightly
export RUSTFLAGS="-C target-cpu=native -C codegen=1"
cargo build --release --target=x86_64-unknown-linux-gnu --locked -Z unstable-options
```
These options result in very little spread and almost the best CPU scores. The build time is only 15 minutes on a modern machine, compared to almost double for LTO builds. 

{{< hint type=tip >}}
The option ``target-cpu=native`` selects the best CPU optimization for the CPU that runs the compiler. If you want to compile for a different CPU, you need to specify the architecture.
{{< /hint >}}

{{< hint type=important >}}
For a different CPU (Intel or AMD), the optimal build could be different.
{{< /hint >}}
