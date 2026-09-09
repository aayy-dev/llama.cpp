# ci branch

Automation branch for this fork. Holds no llama.cpp source.

`master` stays a clean mirror of `ggml-org/llama.cpp` so it can always be
fast-forwarded (`gh repo sync U-rTrDD-fyi/llama.cpp`). All CI lives here
instead, which also keeps the ~50 inherited upstream workflows from firing.

## build-all.yml

Runs every 6 hours on the self-hosted Windows runner `aayush-pc`
(RTX 5060 Ti + RTX 5070, Blackwell sm_120).

1. `check`  - resolves upstream `master` sha, compares to `state/last-built-sha.txt`
2. `build`  - on change, builds cpu / cuda / vulkan (one at a time), runs `ctest -L main`,
              uploads a zip per backend
3. `record` - writes the built sha back to `state/last-built-sha.txt`

Trigger by hand from the Actions tab; `force` rebuilds an unchanged sha and
`ref` builds a specific branch/tag/sha.

## Local build

    call "C:\Program Files\Microsoft Visual Studio\18\Enterprise\VC\Auxiliary\Build\vcvars64.bat"
    cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release -DLLAMA_BUILD_BORINGSSL=ON [-DGGML_CUDA=ON | -DGGML_VULKAN=ON]
    cmake --build build -j %NUMBER_OF_PROCESSORS%

The shell must be **x64** (`vcvars64.bat`). A default `x86` VS shell fails.
