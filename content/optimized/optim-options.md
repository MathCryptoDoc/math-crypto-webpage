---
title: "Optimization options"
weight: 1
---

**!! Page WIP !!**

## How to specify options

Build options can be specified as the environment variable
```
export RUSTFLAGS="-C option1=value1 -C option2=value2"
```
before running ``cargo build``.

Another possibility is to collect all options in a custom profile that is specified in the ``Cargo.toml`` file at the root of the polkadot directory. In the polkadot source, there are already two such profiles:
```
[profile.release]
# Polkadot runtime requires unwinding.
panic = "unwind"
opt-level = 3

[profile.production]
inherits = "release"
lto = true
codegen-units = 1
```
As explained below, the production profile includes more advanced optimization options.

## Opt-level


## [Codegen](https://doc.rust-lang.org/rustc/codegen-options/index.html#codegen-units)
The maximum number of *code generation units* a crate can be split into. Standard value is 16. Choosing 1 may improve the performance of generated code.

In our testing, ``codegen=True`` means ``-C codegen=1``, whereas ``codegen=False`` does not specify it explicitly and thus takes the default value.



## [LTO](https://doc.rust-lang.org/rustc/codegen-options/index.html#lto)

Let LLVM *optimize the whole code during linking*. Three modes can be set with ``-C lto=?`:
- ``no`` LTO: self-explanatory
- ``fat`` LTO: optimizations across whole code; longest build time
- ``thin`` LTO: compromise between fat and disabled; significantly faster build time

If the option ``-C lto`` is not provided or not specified with the options above, [LTO can still be enabled](https://doc.rust-lang.org/rustc/codegen-options/index.html#lto). To avoid ambiguities, we always specify the LTO options in our optimized builds. 


Remark: LTO (thin or fat) does not always lead to improvement. Furthermre, while fat lto is much slower to compile it is not always better than thin LTO. See [here](http://blog.llvm.org/2016/06/thinlto-scalable-and-incremental-lto.html) for details.

Remark: Specifying lto in RUSTFLAGS does not work. Results in error ``error: lto can only be run for executables, cdylibs and static library outputs``. Hence, need to specify in Cargo.toml

``codegen=True`` means ``-C codegen=1``, whereas ``codegen=False`` does not specify it explicitly and thus takes the default value.

## Architecture

Can not be in ``polkadot/Cargo.toml``. Must be a rust flag

?? can as target-cpu = native

