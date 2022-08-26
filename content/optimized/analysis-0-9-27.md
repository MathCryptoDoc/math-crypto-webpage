---
title: "Analysis v0.9.27"
weight: -11
---

We investigated the benchmark scores on a recent CPU ([Intel i7-12700](https://cpu.userbenchmark.com/SpeedTest/1750830/12th-Gen-IntelR-CoreTM-i7-12700)) with NVMe drive for polkadot version 0.9.27. Compared to our [previous analysis](/optimized/analysis-0-9-26), there were a **few changes on the compilation procedure**:
- Options are specified in the ``Cargo.toml`` file (as an overwritten production profile).
- We can reliably use ``lto=off``, ``thin``, or ``fat``.
- Added ``opt-level=2`` or ``3`` as option. 
- The ``code-units`` option is now ``1`` or ``16``.
- No more production or release profile, since we have modified our own profile.
- Only the native architecture is compiled (here, ``alderlake``) or no architecture.

{{< hint type=note >}}
The maximum code optimization is ``opt-level=3``. As suggested [here](https://doc.rust-lang.org/cargo/reference/profiles.html#opt-level), lowering this value to ``opt-level=2`` might produce better results. This is counterintuitive but, as we will see, it leads indeed to better results (sometimes). Similarly, ``lto=fat``, while expensive to build, does not always lead to the fastest code; see [here](http://blog.llvm.org/2016/06/thinlto-scalable-and-incremental-lto.html).
{{< /hint >}}

{{< hint type=important >}}
The same analysis explained on this page was also performed on a Ryzen 5 3600. Even though this is a less powerful processor, compiling yourself brings tremendous improvements (20% and 10%, resp.) for the SR25519-Verify score and the timing of the Remark Extrinsic. See the [notebook](https://github.com/MathCryptoDoc/polkadot-optimized/blob/main/notebook/analyze-0-9-27-Ryzen3600.ipynb) for details.
{{< /hint >}}

The result are 50 different *builds* (including the official polkadot binary and docker image) that are listed below:

{{< expand "Build options" "Click to expand..." >}}
{{< bootstrap-table table_class="table table-dark table-striped table-bordered" >}}
| nb_build   | arch      | toolchain   |   codegen-units | lto        |   opt-level |
|:-----------|:----------|:------------|----------------:|:-----------|------------:|
| 0          | none      | stable      |               1 | off        |           2 |
| 1          | none      | stable      |               1 | off        |           3 |
| 10         | none      | stable      |              16 | thin       |           2 |
| 11         | none      | stable      |              16 | thin       |           3 |
| 12         | alderlake | stable      |               1 | off        |           2 |
| 13         | alderlake | stable      |               1 | off        |           3 |
| 14         | alderlake | stable      |               1 | fat        |           2 |
| 15         | alderlake | stable      |               1 | fat        |           3 |
| 16         | alderlake | stable      |               1 | thin       |           2 |
| 17         | alderlake | stable      |               1 | thin       |           3 |
| 18         | alderlake | stable      |              16 | off        |           2 |
| 19         | alderlake | stable      |              16 | off        |           3 |
| 2          | none      | stable      |               1 | fat        |           2 |
| 20         | alderlake | stable      |              16 | fat        |           2 |
| 21         | alderlake | stable      |              16 | fat        |           3 |
| 22         | alderlake | stable      |              16 | thin       |           2 |
| 23         | alderlake | stable      |              16 | thin       |           3 |
| 24         | none      | nightly     |               1 | off        |           2 |
| 25         | none      | nightly     |               1 | off        |           3 |
| 26         | none      | nightly     |               1 | fat        |           2 |
| 27         | none      | nightly     |               1 | fat        |           3 |
| 28         | none      | nightly     |               1 | thin       |           2 |
| 29         | none      | nightly     |               1 | thin       |           3 |
| 3          | none      | stable      |               1 | fat        |           3 |
| 30         | none      | nightly     |              16 | off        |           2 |
| 31         | none      | nightly     |              16 | off        |           3 |
| 32         | none      | nightly     |              16 | fat        |           2 |
| 33         | none      | nightly     |              16 | fat        |           3 |
| 34         | none      | nightly     |              16 | thin       |           2 |
| 35         | none      | nightly     |              16 | thin       |           3 |
| 36         | alderlake | nightly     |               1 | off        |           2 |
| 37         | alderlake | nightly     |               1 | off        |           3 |
| 38         | alderlake | nightly     |               1 | fat        |           2 |
| 39         | alderlake | nightly     |               1 | fat        |           3 |
| 4          | none      | stable      |               1 | thin       |           2 |
| 40         | alderlake | nightly     |               1 | thin       |           2 |
| 41         | alderlake | nightly     |               1 | thin       |           3 |
| 42         | alderlake | nightly     |              16 | off        |           2 |
| 43         | alderlake | nightly     |              16 | off        |           3 |
| 44         | alderlake | nightly     |              16 | fat        |           2 |
| 45         | alderlake | nightly     |              16 | fat        |           3 |
| 46         | alderlake | nightly     |              16 | thin       |           2 |
| 47         | alderlake | nightly     |              16 | thin       |           3 |
| 5          | none      | stable      |               1 | thin       |           3 |
| 6          | none      | stable      |              16 | off        |           2 |
| 7          | none      | stable      |              16 | off        |           3 |
| 8          | none      | stable      |              16 | fat        |           2 |
| 9          | none      | stable      |              16 | fat        |           3 |
| official   | none      | nightly     |              16 | thin local |           3 |
| docker     | none      | nightly     |              16 | thin local |           3 |
{{< /bootstrap-table >}}
{{< /expand >}}

Rust versions used were ``stable=1.62.1`` and ``nightly=1.65.0-nightly (2befdefdd 2022-08-06)``.

For each build, we repeated the following benchmark 20 times:
```Shell
polkadot benchmark machine --disk-duration 30
```
In addition, compared to the [previous analysis](/optimized/analysis-0-9-26), we added the execution speed of the ``remark`` extrinsic as extra score:
```Shell
polkadot benchmark extrinsic --pallet system --extrinsic remark --chain polkadot-dev
```
This test was repeated 4 times (since it already has its own set of repetitions with each call).

Total CPU utilization before and after each test was negligible (< 1%) to make sure that the benchmark was not disturbed by competing CPU tasks.

{{< hint type=tip >}}
You can repeat these experiments (on your machine) by using the source files on [our Github page](https://github.com/MathCryptoDoc/polkadot-optimized/blob/main/doc/v0.9.27.md).
{{< /hint >}}


## All comparisons

Below we plot the scores for each build in a [box plot](https://en.wikipedia.org/wiki/Box_plot). The red line indicates the median and the box starts from the first to the third quartile values of the scores. Outliers are indicated with circles.

We first compare **CPU scores**. There are clear differences visible but there is no one winning build. We will investigate good build options below.

![CPU scores](scores_cpu_i7_12700.svg)

Regarding **disk scores**, all builds behave very similarly except the docker image, which is worse for random write.

![Disk scores](scores_disk_i7_12700.svg)

For **memory score**, the situation is similar to random write (only docker underperformed).

{{< figure src="scores_mem_i7_12700.svg" width="50%" alt="Memory scores" >}}

Finally, the **extrinsic timing** is similar to the **CPU scores**, except that *lower is better here!*

{{< figure src="extr_remark_i7_12700.svg" width="50%" alt="Timing extrinsic" >}}


## Preliminary conclusions

**Same conclusions** as for [v0.9.26](/optimized/analysis-0-9-26):
- Optimization has little impact on disk and memory scores (except for docker)
- Docker is still penalized here for copy and rnd write.
- Optimizing has a potential big influence on CPU scores and timing of an extrinsic.

**New information**:
- There are 8 builds (30, 6, 31, 7, 19, 18, 43, 42) that have very bad CPU scores. 

Maybe surprisingly, the **worst builds** do not all simply have ``opt-level = 2``. It seems that ``codegen-units=16`` and ``lto=off`` is the reason, regardless of ``opt-level``.

{{< bootstrap-table table_class="table table-dark table-striped table-bordered" >}}
|   nb_build | arch      | toolchain   |   codegen-units | lto   |   opt-level |
|-----------:|:----------|:------------|----------------:|:------|------------:|
|         18 | alderlake | stable      |              16 | off   |           2 |
|         19 | alderlake | stable      |              16 | off   |           3 |
|         30 | none      | nightly     |              16 | off   |           2 |
|         31 | none      | nightly     |              16 | off   |           3 |
|         42 | alderlake | nightly     |              16 | off   |           2 |
|         43 | alderlake | nightly     |              16 | off   |           3 |
|          6 | none      | stable      |              16 | off   |           2 |
|          7 | none      | stable      |              16 | off   |           3 |
{{< /bootstrap-table >}}



## Finding good optimization options

We will now find build options that have **good performance for the two CPU scores and the time for an extrinsic**. 

Since there is not one build that wins in all these three scores, we identify the Pareto efficient builds. In case of only two scores, determining the [Pareto front](https://en.wikipedia.org/wiki/Pareto_front) can be done by hand on a scatter plot. Since our test has three scores, we compute these points algorithmically instead; details are in the [Python notebook](https://github.com/MathCryptoDoc/polkadot-optimized/blob/main/notebook/analyze-0-9-26.ipynb). Due to statistical errors on the scores, we also find all builds that are **close to these Pareto efficient builds**. To that end, we define a box around each score with width equal to its statistical error. A build is included if its box overlaps to that of a Pareto efficient build. Again, details are in the [Python notebook](https://github.com/MathCryptoDoc/polkadot-optimized/blob/main/notebook/analyze-0-9-27.ipynb).

This gives us the following winning optimization options:

{{< bootstrap-table table_class="table table-dark table-striped table-bordered" >}}
|   nb_build | arch      | toolchain   |   codegen-units | lto   |   opt-level |
|-----------:|:----------|:------------|----------------:|:------|------------:|
|         15 | alderlake | stable      |               1 | fat   |           3 |
|         17 | alderlake | stable      |               1 | thin  |           3 |
|         21 | alderlake | stable      |              16 | fat   |           3 |
|         38 | alderlake | nightly     |               1 | fat   |           2 |
|         40 | alderlake | nightly     |               1 | thin  |           2 |
|         41 | alderlake | nightly     |               1 | thin  |           3 |
|         45 | alderlake | nightly     |              16 | fat   |           3 |
|         47 | alderlake | nightly     |              16 | thin  |           3 |
{{< /bootstrap-table >}}

and corresponding box plots:

![CPU scores](pareto_scores_i7_12700.svg)

**Interesting**: build 47 is best for BLAKE2 but *very* bad for Extrinsic and Verify. It is even worse then the offical polkadot binary! The optimization options do not predict this bad behavior: ``lto=thin`` and ``codegen-units=16`` could actually be good since it can use lto across the 16 crates. Build 45 switches this to ``lto=fat`` with dramatically better performance!

**What are the winning builds?** 
- Surprisingly, ``opt-level=3`` is not always needed. However, some ``lto`` is required.
- In fact, build 38 (``opt-level=2``) has excellent SR25519-Verify scores with zero variance and very good Extrinsic score.
- Build 45 is best for SR25519-Verify and Extrinsic, and third best for BLAKE2-256. Except for its high variance, build 45 would be a clear winner. It uses ``codegen-units=16``, which is a little surprising.
- Builds 15, 21, 38, and 40 are good mixes between both scores.

Throwing away builds that do not improve upon the official binary, **we have 15, 21, 38, 40, 45 as the good builds**. They all build with `target-cpu=native``.

{{< bootstrap-table table_class="table table-dark table-striped table-bordered" >}}
| nb_build   |   BLAKE2-256 |   relative diff (%) |   SR25519-Verify |   relative diff (%) |   Extr-Remark |   relative diff (%) | toolchain   |   codegen-units | lto   |   opt-level |
|:-----------|-------------:|--------------------:|-----------------:|--------------------:|--------------:|--------------------:|:------------|----------------:|:------|------------:|
| 15         |         1400 |                 4.5 |           1010.2 |                 0.9 |       54660   |                -1.1 | stable      |               1 | fat   |           3 |
| 21         |         1400 |                 4.5 |           1020.1 |                 1.9 |       55021.5 |                -0.5 | stable      |              16 | fat   |           3 |
| 38         |         1400 |                 4.5 |           1020   |                 1.9 |       54510.5 |                -1.4 | nightly     |               1 | fat   |           2 |
| 40         |         1410 |                 5.2 |           1005   |                 0.4 |       54947   |                -0.6 | nightly     |               1 | thin  |           2 |
| 45         |         1400 |                 4.5 |           1022.9 |                 2.1 |       54285.5 |                -1.8 | nightly     |              16 | fat   |           3 |
| official   |         1340 |                 0   |           1001.4 |                 0   |       55277.5 |                 0   | nightly         |             ? | ?   |         3 |
| docker     |         1340 |                 0   |           1002.6 |                 0.1 |       55372   |                 0.2 | nightly         |             ? | ?   |         3 |
{{< /bootstrap-table >}}


## Winning optimization options

Based on the above analysis, we subjectively choose 38 and 45 as best builds. To compile polkadot with them, you need to modify the production profile in the ``Cargo.toml``. For build 38:
```TOML
[profile.production]
inherits = "release"
codegen-units = 1
lto = "fat"
opt-level = 2
```
and for build 45:
```TOML
[profile.production]
inherits = "release"
codegen-units = 16
lto = "fat"
opt-level = 3
```
Afterwards, you build as usual will the command
```Shell
rustup override set nightly
export RUSTFLAGS="-C target-cpu=native"
cargo build --profile=production --target=x86_64-unknown-linux-gnu --locked -Z unstable-options
```
Please see our convenient [Python script](https://github.com/MathCryptoDoc/polkadot-optimized/blob/main/compile.py) if instead you want to build several binaries and specify options in a simpler way.

{{< hint type=tip >}}
The option ``target-cpu=native`` selects the best CPU optimization for the CPU that runs the compiler. If you want to compile for a different CPU, you need to specify the architecture.
{{< /hint >}}

{{< hint type=important >}}
For a different CPU (Intel or AMD), the optimal build could be different.
{{< /hint >}}
