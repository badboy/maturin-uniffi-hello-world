# A Maturin+UniFFI example

Currently a bit broken.

## Versions

```
$ maturin --version
maturin 0.14.10
$ uniffi-bindgen --version
uniffi-bindgen 0.22.0
```

## Setup

```
python -mvenv .venv
. .venv/bin/activate
```

## Develop

```
$ maturin develop
🔗 Found uniffi bindings
💻 Using `MACOSX_DEPLOYMENT_TARGET=11.0` for aarch64-apple-darwin by default
    Finished dev [unoptimized + debuginfo] target(s) in 0.02s
📦 Built wheel to /tmp/hello_world-0.1.0-py3-none-macosx_11_0_arm64.whl
🛠 Installed hello-world-0.1.0
```

Directory layout after this:

```
❯ tree python
python
└── hello_world
    ├── __init__.py
    └── hello_world_ffi
        ├── __init__.py
        ├── hello_world.py
        └── libuniffi_hello_world.dylib
```

Running it:

```
$ python3 -c 'import hello_world'
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "/src/uniffi-hello-world/python/hello_world/__init__.py", line 3, in <module>
    from ._uniffi import add
ModuleNotFoundError: No module named 'hello_world._uniffi'
```

## Build

```
$ maturin build
🍹 Building a mixed python/rust project
🔗 Found uniffi bindings
💻 Using `MACOSX_DEPLOYMENT_TARGET=11.0` for aarch64-apple-darwin by default
    Finished dev [unoptimized + debuginfo] target(s) in 0.02s
📦 Built wheel to /src/uniffi-hello-world/target/wheels/hello_world-0.1.0-py3-none-macosx_11_0_arm64.whl
```

Layout:

```
$ unzip -l target/wheels/hello_world-0.1.0-py3-none-macosx_11_0_arm64.whl
Archive:  target/wheels/hello_world-0.1.0-py3-none-macosx_11_0_arm64.whl
  Length      Date    Time    Name
---------  ---------- -----   ----
     3260  01-18-2023 09:18   hello_world-0.1.0.dist-info/METADATA
      103  01-18-2023 09:18   hello_world-0.1.0.dist-info/WHEEL
       63  01-18-2023 09:18   hello_world/__init__.py
       35  01-18-2023 09:18   hello_world/_uniffi/__init__.py
    13434  01-18-2023 09:18   hello_world/_uniffi/hello_world.py
   590533  01-18-2023 09:18   hello_world/_uniffi/libuniffi_hello_world.dylib
      581  01-18-2023 09:18   hello_world-0.1.0.dist-info/RECORD
---------                     -------
   608009                     7 files
```

Running it:

```
$ pip install target/wheels/hello_world-0.1.0-py3-none-macosx_11_0_arm64.whl
[...]
Successfully installed hello-world-0.1.0
$ python3 -c 'import hello_world'
42
```

## What's wrong?

[Bug Report and patch][pr].

`maturin develop` creates the library in a subdirectory named after the module,
whereas `maturin build` correctly uses the name defined using `package.metadata.maturin`.

With [PR 1416][pr] merged this will be fixed.

[pr]: https://github.com/PyO3/maturin/pull/1416
