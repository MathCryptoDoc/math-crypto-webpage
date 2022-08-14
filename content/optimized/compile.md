---
title: "Compile instructions"
weight: -10
---
The [official polkadot binaries](https://github.com/paritytech/polkadot/releases) are reasonably optimized but they are not targeting specific CPU architectures. The same is true for the [official docker image](https://hub.docker.com/r/parity/polkadot) since they inject the official binary.

Compiling your own binary with optimizations for the architecture of your validator node is not that difficult. The explanation below is adapted from [Parity's github page](https://github.com/paritytech/polkadot#building).

{{< hint type=tip >}}
On a modern CPU an optimized compiled binary can have up to 6% better CPU scores relative to the official binary. See [here]({{< ref "/optimized/analysis-0-9-26" >}} "Detailed analysis") for the detailed results with an Intel i7-12700.
{{< /hint >}}

{{< hint type=important >}}
Compiling polkadot requires a fairly recent OS to work out of the box. The following instructions are confirmed to work on Ubuntu 22.04.
{{< /hint >}}


## Install libraries

One time only: install required libraries and rust

```Shell
sudo apt install cmake clang lld build-essential git libclang-dev pkg-config libssl-dev
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
```

## Get new release and compile
With each new polkadot release: Update rust
```Shell
rustup update
```
Get the source files of the official polkadot release version ``VER``. Beware: The commands below remove the old files!
```Shell
export VER=0.9.26
mkdir -p ~/optimized_polkadot
cd ~/optimized_polkadot
rm -rf polkadot
git clone --depth 1 --branch v${VER} https://github.com/paritytech/polkadot.git

cd polkadot
./scripts/init.sh
cargo fetch
```

Choose your optimization options. The options below work well on our test machine (i7-12700). See [here]({{< ref "/optimized/optim-options" >}} "Optimization options") for more details and which options you can choose
```Shell
rustup override set nightly
export RUSTFLAGS="-C target-cpu=native -C codegen=1"
cargo build --release --target=x86_64-unknown-linux-gnu --locked -Z unstable-options
```
Compiling takes a little while. On a fast machine with NVMe storage, it is about 15 minutes.	

## Benchmark and deploy

Benchmark your optimized binary ``polkadot_opt`` to make sure it runs OK.
```Shell
cp target/x86_64-unknown-linux-gnu/release/polkadot ~/optimized_polkadot/polkadot_opt
cd ~/optimized_polkadot
polkadot_opt benchmark machine --disk-duration 30
```
Optional: compare with the official binary to see if it was worth the trouble.
```Shell
cd ~/optimized_polkadot/
wget https://github.com/paritytech/polkadot/releases/download/v${VER}/polkadot
chmod +x polkadot
polkadot benchmark machine --disk-duration 30
```
If you are happy, deploy the optimized binary. With systemd, you can simply replace the official binary with the optimized one and restart the service. For docker, you will need to build your own docker image.


## Problems
- New polkadot versions can require newer version of system libraries and programs (for example, ``cmake``). Updating your machine with ``sudo apt update`` is advisable.


