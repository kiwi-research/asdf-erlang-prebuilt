# asdf-erlang-prebuilt

An [asdf](https://asdf-vm.com/) plugin for Erlang/OTP that downloads prebuilt binaries, with a source-build fallback.

## Prebuilt Sources

| Platform | Source | Architectures |
|----------|--------|---------------|
| macOS | [erlef/otp_builds](https://github.com/erlef/otp_builds) | x86_64, aarch64 (Apple Silicon) |
| Linux (Ubuntu/Debian) | [hex.pm/builds/otp](https://builds.hex.pm/builds/otp) | amd64, arm64 |

On unsupported platforms (Alpine, Fedora, Arch, etc.) or when prebuilt binaries are incompatible, the plugin automatically falls back to building from source using [kerl](https://github.com/kerl/kerl).

## Installation

### With asdf

```bash
asdf plugin add erlang https://github.com/kiwi-research/asdf-erlang-prebuilt.git
asdf install erlang 27.0
```

### With proto (asdf backend)

In `.prototools`:

```toml
"asdf:erlang" = "28.2"
"asdf:elixir" = "1.19.5-otp-28"

[tools."asdf:erlang"]
asdf-repository = "https://github.com/kiwi-research/asdf-erlang-prebuilt"
```

Elixir uses the standard [asdf-elixir](https://github.com/asdf-vm/asdf-elixir) plugin via proto's built-in asdf backend — no custom plugin config needed. The `-otp-28` suffix selects a build compiled against the matching OTP version.

## Fallback Behavior

If a prebuilt binary is not available or not compatible with the current system, the plugin automatically falls back to building from source using [kerl](https://github.com/kerl/kerl). This happens when:

- The prebuilt download returns HTTP 404 or 403
- The prebuilt binary fails to execute (e.g., glibc incompatibility on non-Ubuntu distros)

Source builds require these dependencies:

- autoconf
- build-essential (or equivalent: `base-devel` on Arch, `alpine-sdk` on Alpine)
- libssl-dev (or `openssl-dev`)
- libncurses-dev (or `ncurses-dev`)

## Platform Detection

### Linux

- **Architecture**: Detected via `dpkg --print-architecture`, falls back to `uname -m`
- **Ubuntu version**: For hex.pm prebuilt URL construction:
  - Ubuntu: uses actual `VERSION_ID`
  - Debian 10/11/12/13+: maps to Ubuntu 18.04/20.04/22.04/24.04
  - Other distros: defaults to Ubuntu 24.04
  - Override with `ASDF_ERLANG_UBUNTU_RELEASE` env var

### macOS

- **Architecture**: Detected via `uname -m` (`x86_64` or `arm64`/`aarch64`)

## Known Issues

### Proto shim registry bug

When using proto's asdf backend, the primary shims (`elixir`, `erlang`) don't get their `parent` field set in `~/.proto/shims/registry.json`. This means running `elixir --version` via the shim fails, while `mix`, `iex`, `erl`, `erlc` all work fine.

**Workaround**: Manually edit `~/.proto/shims/registry.json`:
```json
"elixir": { "parent": "asdf:elixir" },
"erlang": { "parent": "asdf:erlang" }
```

<!-- TODO: File upstream issue at https://github.com/moonrepo/proto -->
