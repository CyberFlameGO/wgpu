<img align="right" width="25%" src="logo.png">

# wgpu

[![Matrix](https://img.shields.io/badge/Dev_Matrix-%23wgpu%3Amatrix.org-blueviolet.svg)](https://matrix.to/#/#wgpu:matrix.org) [![Matrix](https://img.shields.io/badge/User_Matrix-%23wgpu--users%3Amatrix.org-blueviolet.svg)](https://matrix.to/#/#wgpu-users:matrix.org)
[![Build Status](https://github.com/gfx-rs/wgpu/workflows/CI/badge.svg)](https://github.com/gfx-rs/wgpu/actions)
[![codecov.io](https://codecov.io/gh/gfx-rs/wgpu/branch/master/graph/badge.svg?token=84qJTesmeS)](https://codecov.io/gh/gfx-rs/wgpu)

`wgpu` is a cross-platform, safe, pure-rust graphics api. It runs natively on Vulkan, Metal, D3D12, D3D11, and OpenGLES; and on top of WebGPU on wasm.

The api is based on the [WebGPU standard](https://gpuweb.github.io/gpuweb/). It serves as the core of the WebGPU integration in Firefox, Servo, and Deno.

## Repo Overview

The repository hosts the following libraries:

  - [![Crates.io](https://img.shields.io/crates/v/wgpu.svg?label=wgpu)](https://crates.io/crates/wgpu) [![docs.rs](https://docs.rs/wgpu/badge.svg)](https://docs.rs/wgpu/) - User facing Rust API.
  - [![Crates.io](https://img.shields.io/crates/v/wgpu-core.svg?label=wgpu-core)](https://crates.io/crates/wgpu-core) [![docs.rs](https://docs.rs/wgpu-core/badge.svg)](https://docs.rs/wgpu-core/) - Internal WebGPU implementation.
  - [![Crates.io](https://img.shields.io/crates/v/wgpu-hal.svg?label=wgpu-hal)](https://crates.io/crates/wgpu-hal) [![docs.rs](https://docs.rs/wgpu-hal/badge.svg)](https://docs.rs/wgpu-hal/) - Internal unsafe GPU API abstraction layer.
  - [![Crates.io](https://img.shields.io/crates/v/wgpu-types.svg?label=wgpu-types)](https://crates.io/crates/wgpu-types) [![docs.rs](https://docs.rs/wgpu-types/badge.svg)](https://docs.rs/wgpu-types/) - Rust types shared between all crates.
  - [![Crates.io](https://img.shields.io/crates/v/deno_webgpu.svg?label=deno_webgpu)](https://crates.io/crates/deno_webgpu) - WebGPU implementation for the Deno JavaScript/TypeScript runtime

The folowing binaries:
  - `cts_runner` - WebGPU Conformance Test Suite runner using `deno_webgpu`.
  - `player` - standalone application for replaying the API traces.
  - `wgpu-info` - program that prints out information about all the adapters on the system or invokes a command for every adapter.

For an overview of all the components in the gfx-rs ecosystem, see [the big picture](./etc/big-picture.png).

## Getting Started

### Rust

Rust examples can be found at `wgpu/examples`. You can run the examples with `cargo run --example name`. See the [list of examples](wgpu/examples). For detailed instructions, look at our [Get Started](https://github.com/gfx-rs/wgpu/wiki/Getting-Started) wiki.

If you are looking for a wgpu tutorial, look at the following:
- https://sotrh.github.io/learn-wgpu/

### C/C++

To use wgpu in C/C++, you need [wgpu-native](https://github.com/gfx-rs/wgpu-native). 

### Others

If you want to use wgpu in other languages, there are many bindings to wgpu-native from languages such as Python, D, Julia, Kotlin, and more. See [the list](https://github.com/gfx-rs/wgpu-native#bindings).

## Community

We have three different matrix rooms that form the wgpu community:
- [![Matrix](https://img.shields.io/badge/Dev_Matrix-%23wgpu%3Amatrix.org-blueviolet.svg)](https://matrix.to/#/#wgpu:matrix.org) - Discussion of the library's development.
- [![Matrix](https://img.shields.io/badge/User_Matrix-%23wgpu--users%3Amatrix.org-blueviolet.svg)](https://matrix.to/#/#wgpu-users:matrix.org) - Discussion of using the library and the surrounding ecosystem.
- [![Matrix](https://img.shields.io/badge/Random_Matrix-%23wgpu--random%3Amatrix.org-blueviolet.svg)](https://matrix.to/#/#wgpu-random:matrix.org) - Discussion of everything else.

## Wiki

We have a [wiki](https://github.com/gfx-rs/wgpu/wiki) that serves as a knowledge base.

## Supported Platforms

   API   |    Windows                    |  Linux & Android   |    macOS & iOS     |
  -----  | ----------------------------- | ------------------ | ------------------ |
  Vulkan | :white_check_mark:            | :white_check_mark: |                    |
  Metal  |                               |                    | :white_check_mark: |
  DX12   | :white_check_mark: (W10 only) |                    |                    |
  DX11   | :construction:                |                    |                    |
  GLES3  |                               | :ok:               |                    |

:white_check_mark: = First Class Support — :ok: = Best Effort Support — :construction: = Unsupported, but support in progress

## Environment Variables

All testing and example infrastructure shares the same set of environment variables that determine which Backend/GPU it will run on.

- `WGPU_ADAPTER_NAME` with a substring of the name of the adapter you want to use (ex. `1080` will match `NVIDIA GeForce 1080ti`).
- `WGPU_BACKEND` with a comma separated list of the backends you want to use (`vulkan`, `metal`, `dx12`, `dx11`, or `gl`).
- `WGPU_POWER_PREFERENCE` with the power preference to choose when a specific adapter name isn't specified (`high` or `low`)

When running the CTS, use the variables `DENO_WEBGPU_ADAPTER_NAME`, `DENO_WEBGPU_BACKEND`, `DENO_WEBGPU_POWER_PREFERENCE`.

## Testing

We have multiple methods of testing, each of which tests different qualities about wgpu. We automatically run our tests on CI if possible. The current state of CI testing:

| Backend/Platform | Status                                                                 |
| ---------------- | ---------------------------------------------------------------------- |
| DX12/Windows 10  | :heavy_check_mark: (over WARP)                                         |
| DX11/Windows 10  | :construction: (over WARP)                                             |
| Metal/MacOS      | :x: (no CPU runner)                                                    |
| Vulkan/Linux     | :ok: ([cts hangs](https://github.com/gfx-rs/wgpu/issues/1974))         |
| GLES/Linux       | :x: ([egl fails init](https://github.com/gfx-rs/wgpu/issues/1551))     |

### Core Test Infrastructure

All framework based examples have image comparison tested against their screenshot. 

To run the test suite on the default device:

```
cargo test --no-fail-fast
```

There's logic which can automatically run the tests once for each adapter on your system.

```
cargo run --bin wgpu-info -- cargo test --no-fail-fast
```

Then to run an example's image comparison tests, run:

```
cargo test --example <example-name> --no-fail-fast
```

Or run a part of the integration test suite:

```
cargo test -p wgpu -- <name-of-test>
```

If you are a user and want a way to help contribute to wgpu, we always need more help writing test cases. 

### WebGPU Conformance Test Suite

WebGPU includes a Conformance Test Suite to validate that implementations are working correctly. We can run this CTS against wgpu.

To run the CTS, first you need to check it out:

```
git clone https://github.com/gpuweb/cts.git
cd cts
# works in bash and powershell
git checkout $(cat ../cts_runner/revision.txt)
```

To run a given set of tests:

```
# Must be inside the cts folder we just checked out, else this will fail
cargo run --manifest-path ../cts_runner/Cargo.toml --frozen -- ./tools/run_deno --verbose "<test string>"
```

To find the full list of tests, go to the [online cts viewer](https://gpuweb.github.io/cts/standalone/?runnow=0&worker=0&debug=0&q=webgpu:*). 

The list of currently enabled CTS tests can be found [here](./cts_runner/test.lst).

## Coordinate Systems

wgpu uses the coordinate systems of D3D and Metal:

Render | Depth | Texture
-------|-------|--------
![render_coordinates](./etc/render_coordinates.png) | ![depth_coordinates](./etc/depth_coordinates.png) | ![texture_coordinates](./etc/texture_coordinates.png)
