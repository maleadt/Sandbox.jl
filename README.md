# Sandbox.jl

> The cultured host's toolkit for ill-mannered Linux guests.

This package provides basic containerization tools for running Linux guests on a variety of platforms.
As of the time of writing, it supports two execution backends:

* A Linux User Namespaces executor, which is very fast and lightweight

* A Docker executor which is slower, but more compatible (it works on macOS, and may work on Windows)

The executors are responsible for running/virtualizing a given `Cmd` within a root filesystem that is defined by the user, along with various paths that can be mounted within the sandbox.
These capabilities were originally built for [BinaryBuilder.jl](https://github.com/JuliaPackaging/BinaryBuilder.jl), however this functionality is now mature enough that it may be useful elsewhere.

## Basic usage

To make use of this toolkit, you will need to have a root filesystem image that you want to use.
This package comes with an Alpine minimal rootfs that can be used for quick tests, to launch `/bin/sh` in an interactive shell, run the following:

```julia
using Sandbox

config = SandboxConfig(
    Dict("/" => Sandbox.alpine_rootfs());
    stdin=Base.stdin,
    stdout=Base.stdout,
    stderr=Base.stderr,
)
with_executor() do exe
    run(exe, config, `/bin/sh`)
end
```

While this launches an interactive session due to hooking up `stdout`/`stdin`, one can easily capture output by setting `stdout` to an `IOBuffer`, or even a `PipeBuffer` to chain together multiple processes from different sandboxes.