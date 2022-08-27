---
title: "Summary v0.9.27"
weight: -12
---

Based on our [detailed analysis](/optimized/analysis-0-9-27), we have compiled polkadot version 0.9.27 by modifying the ``production`` profile in the ``Cargo.toml`` root folder of the polkadot source code.


In the table below, the **benchmark scores** (*higher* is better) and the **timing of the Remark Extrinsic** (*lower* is better) are listed for a few machines with different CPU architectures. Please look [here](/optimized/analysis-0-9-27) on why we only focus on these values and how many repetitions were done to reduce the statistical errors.

{{< hint type=important >}}
The tables below are not well visible on mobile. Please use the desktop version.
{{< /hint >}}

{{< hint type=note >}}
The column **impr. (%)** is the *relative* improvement in percentage compared to the official polkadot binary *on the same machine*.
{{< /hint >}}



### AMD Ryzen 5 3600 (znver2)

While not very powerful nor recent, this cpu is quite popular. **It benefits a lot from optimization: 25% and 10% improvement for SR25519-Verify and Extrinsic timing**, resp., at the expense of -1% for BLAKE2-256.

{{< bootstrap-table table_class="table table-dark table-striped table-bordered" >}}
|  build   |   BLAKE2-256 |   impr. (%) |   SR25519-Verify |   impr. (%) |   Extr-Remark |   impr. (%) | toolchain   |   codegen-units | lto        |   opt-level |
|:-----------|-------------:|--------------------------:|-----------------:|------------------------------:|--------------:|---------------------------:|:------------|----------------:|:-----------|------------:|
| 4          |        994.2 |                      -1.6 |            697.4 |                          28.2 |         85519 |                       12.6 | stable      |               1 | fat        |           2 |
| 7          |       1003.5 |                      -0.6 |            684   |                          25.7 |         86683 |                       11.4 | stable      |               1 | thin       |           3 |
| official   |       1010   |                           |            544.1 |                               |         97889 |                            | nightly     |              16 | thin local |           3 |
| docker     |        972.1 |                      -3.7 |            531   |                          -2.4 |        100515 |                       -2.7 | nightly     |              16 | thin local |           3 |
| production         |        959.7 |                      -5   |            703.1 |                          29.2 |         85197 |                       13   | nightly     |               1 | fat        |      3 |
{{< /bootstrap-table >}}

### Intel i7 12700 (skylake)

A very recent cpu with excellent performance for the official binary. **Optimization improves all the numbers but the effect is limited**: about 2% to 5%.

{{< bootstrap-table table_class="table table-dark table-striped table-bordered" >}}
|  build   |   BLAKE2-256 |   impr. (%) |   SR25519-Verify |   impr. (%) |   Extr-Remark |   impr. (%) | toolchain   |   codegen-units | lto        |   opt-level |
|:-----------|-------------:|--------------------------:|-----------------:|------------------------------:|--------------:|---------------------------:|:------------|----------------:|:-----------|------------:|
| 38         |         1400 |                       4.5 |           1020   |                           1.9 |         54505 |                        1.4 | nightly     |               1 | fat        |           2 |
| 45         |         1400 |                       4.5 |           1022.9 |                           2.1 |         54256 |                        1.8 | nightly     |              16 | fat        |           3 |
| 14         |         1400 |                       4.5 |           1020   |                           1.9 |         55013 |                        0.5 | stable      |               1 | fat        |           2 |
| official   |         1340 |                          |           1001.4 |                              |         55263 |                          | nightly         |             16 | thin local        |         3 |
| docker     |         1340 |                       0   |           1002.6 |                           0.1 |         55423 |                       -0.3 | nightly     |              16 | thin local |           3 |
| production         |         1350 |                       0.7 |            999.6 |                          -0.2 |         55317 |                       -0.1 | nightly     |               1 | fat        |           3 |
{{< /bootstrap-table >}}


### Intel Xeon E5-2620 v3 (haswell)

An old Xeon from 2014 that would not be adequate for a polkadot validator. **Optimization leads to improved performance in all three scores of about 5% to 15%.**

{{< bootstrap-table table_class="table table-dark table-striped table-bordered" >}}
|  build   |   BLAKE2-256 |   impr. (%) |   SR25519-Verify |   impr. (%) |   Extr-Remark |   impr. (%) | toolchain   |   codegen-units | lto        |   opt-level |
|:-----------|-------------:|--------------------------:|-----------------:|------------------------------:|--------------:|---------------------------:|:------------|----------------:|:-----------|------------:|
| 3          |        711.4 |                       5.3 |            479.5 |                          17   |        129757 |                        3.9 | stable      |               1 | thin       |           3 |
| 9          |        687.4 |                       1.7 |            479.2 |                          16.9 |        124197 |                        8   | stable      |              16 | fat        |           3 |
| official   |        675.7 |                           |            409.9 |                               |        135009 |                         | nightly     |              16 | thin local |           3 |
| production         |        692.1 |                       2.4 |            473.4 |                          15.5 |        124522 |                        7.8 | nightly     |               1 | fat        |           3 |
{{< /bootstrap-table >}}



### How to compile

In order to generate an optimal build, you need to modify the ``Cargo.toml`` file as follows:
```TOML
[profile.production]
inherits = "release"
codegen-units = CHANGE THIS
lto = CHANGE THIS
opt-level = CHANGE THIS
```
where *CHANGE THIS* determines the *build profile* specified above. Afterwards, you build as usual:
```Shell
rustup override set nightly
export RUSTFLAGS="-C target-cpu=native"
cargo build --profile=production --target=x86_64-unknown-linux-gnu --locked -Z unstable-options
```

{{< hint type=note >}}
Instead of manually building, you can also use our convenient [Python script](https://github.com/MathCryptoDoc/polkadot-optimized/blob/main/compile.py). It allows you to build several binaries in one go and specify options in a simpler way.
{{< /hint >}}





If you want to contribute to this list:
- Take a look [here](https://github.com/MathCryptoDoc/polkadot-optimized/blob/main/doc/v0.9.27.md) to install the necessary packages.
- Then you run [our benchmark script](https://github.com/MathCryptoDoc/polkadot-optimized) when there are *no other resource hungry tasks running*.
- Finally, [send us](/staking/contact) the ``feather`` files generated by the parse script.