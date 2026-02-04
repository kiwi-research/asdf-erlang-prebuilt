# asdf-erlang-prebuilt

An [asdf](https://asdf-vm.com/) plugin for Erlang/OTP that downloads prebuilt binaries, with a source-build fallback.

## Prebuilt Sources

| Platform | Source | Architectures |
|----------|--------|---------------|
| macOS | [erlef/otp_builds](https://github.com/erlef/otp_builds) | x86_64, aarch64 (Apple Silicon) |
| Linux (Ubuntu) | [hex.pm/builds/otp](https://builds.hex.pm/builds/otp) | amd64, arm64 |

## Installation

### With asdf

```bash
asdf plugin add erlang https://github.com/kiwi-research/asdf-erlang-prebuilt.git
asdf install erlang 27.0
```

### With proto (asdf backend)

In `.prototools`:

```toml
"asdf:erlang" = "27.0"

[tools."asdf:erlang"]
asdf-repository = "https://github.com/kiwi-research/asdf-erlang-prebuilt"
```

## Fallback Behavior

If a prebuilt binary is not available for your platform/version combination (HTTP 404 or 403), the plugin automatically falls back to building from source using [kerl](https://github.com/kerl/kerl).

Source builds require these dependencies:

- autoconf
- build-essential (or equivalent)
- libssl-dev
- libncurses-dev

## Platform Detection

### Linux

- **Architecture**: Detected via `dpkg --print-architecture`, falls back to `uname -m`
- **Ubuntu version**: Detected via `lsb_release -rs`, falls back to `/etc/os-release`, defaults to `22.04`

### macOS

- **Architecture**: Detected via `uname -m` (`x86_64` or `arm64`/`aarch64`)
