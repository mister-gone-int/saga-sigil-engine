# saga-sigil-engine

Release files for [SagaSigil](https://github.com/mister-gone-int/saga-sigil)'s
captioning engine that don't ship in its installer. Nothing here is built
from this repository: it holds this README, the license, and one release per
build.

## What is here

**The CUDA 12 backend** (`ggml-cuda`): llama.cpp's CUDA backend built with
NVIDIA's CUDA 12.9 toolkit, with machine code for every card CUDA 12 serves
(Maxwell to Blackwell, no PTX). SagaSigil's NVIDIA CUDA 12 download fetches
it, together with NVIDIA's own libraries from NVIDIA's server, for
computers whose driver or card can't run CUDA 13. The CUDA 13 backend isn't
here: it ships inside SagaSigil's installer.

Each release is named for the llama.cpp release it was built from, which is
the one SagaSigil's engine is built from (a backend of another build can't
load beside the engine):

| Release | File | Holds |
|---|---|---|
| `ggml-cuda12-<llama.cpp tag>` | `ggml-cuda12-<tag>-linux-x86_64.tar.xz` | `lib/libggml-cuda.so`, `LICENSE` |

The archive is packed reproducibly (fixed times, owner and order), so the
same build always gives the same bytes. SagaSigil pins each file's URL,
size and SHA-256 in its source (`src-native/src/ai/cuda/sets.rs`) and
refuses a download that doesn't match, so a file replaced here is never
used.

No Windows build is published yet: a Windows computer that gets CUDA 12
runs its captions through Vulkan until one is.

## How a release is made

From the SagaSigil checkout:

1. Build the backend: `npm run build:engine -- --cuda-12 <CUDA 12.9 toolkit>`,
   on Linux inside NVIDIA's `nvidia/cuda:12.9.1-devel-rockylinux8` container
   so it runs on glibc 2.28. It lands in `app-resources/engine-cuda12/`.
2. Publish it: `python3 scripts/publish-engine-cuda.py` checks the build
   (CUDA 12.9, the engine's llama.cpp release, no identifying text) and packs
   it as a dry run; `--push` creates the release here, uploads the archive,
   downloads it back and pins it in `sets.rs`.

Every llama.cpp update of SagaSigil's engine needs a new release.

## License

The backend is llama.cpp's ggml CUDA code, under its MIT license
([`LICENSE`](LICENSE), also inside each archive). It was compiled with
NVIDIA's CUDA toolkit and loads NVIDIA's CUDA runtime and cuBLAS at run time;
those aren't part of these files and come from NVIDIA under NVIDIA's
license.
