# DeepSeek TUI — Termux Edition

![screenshot](image.png)

> A Termux (Android) compatible fork of the DeepSeek TUI terminal coding agent. Based on [DeepSeek-TUI v0.8.28](https://github.com/Hmbown/DeepSeek-TUI) with minimal modifications to compile and run on Android/Termux.

[中文 README](README.zh-CN.md)

---

## What's Different from Upstream

This repo is a Termux compatibility fork of [Hmbown/DeepSeek-TUI](https://github.com/Hmbown/DeepSeek-TUI) v0.8.28. Only essential changes were made:

### 1. Dependency adjustments — `crates/tui/Cargo.toml`

| Change | Reason |
|--------|--------|
| `arboard` made an unconditional dependency (removed `not(target_os = "android")` cfg gate) | `arboard` can compile on Android/Termux using `rustls` — no need to exclude it |
| `reqwest` TLS backend switched from `native-tls` to `rustls` | Android/Termux lacks the OpenSSL system library required by `native-tls`; `rustls` is a pure-Rust TLS implementation with no system deps |

### 2. Clipboard support — `crates/tui/src/tui/clipboard.rs`

- Removed all `#[cfg(not(target_os = "android"))]` conditional compilation guards
- Removed the Android-only no-op clipboard stubs
- Clipboard code now compiles uniformly on all targets, handled by the `arboard` crate

### 3. Browser-launch compatibility — `crates/tui/src/tui/ui.rs`

- Removed a `#[cfg(any(target_os = "macos", target_os = "linux", target_os = "windows"))]` gate on the browser-launch code path
- Allows the function to compile on Android targets (returns a "not supported" error at runtime, but no longer blocks compilation)

### 4. Termux build script — `build_deepseek.sh`

Added a Termux-specific build script that pre-configures the required environment:
```bash
export HOME=/data/data/com.termux/files/home
export PREFIX=/data/data/com.termux/files/usr
export PATH=/data/data/com.termux/files/usr/bin:/data/data/com.termux/files/usr/bin/applets
export LD_LIBRARY_PATH=/data/data/com.termux/files/usr/lib
export TMPDIR=/data/data/com.termux/files/usr/tmp
```

---

## Install on Termux

### Prerequisites

```bash
pkg update && pkg upgrade
pkg install rust binutils git make cmake pkg-config
```

### Clone & Build

```bash
git clone https://github.com/kiluahh/DeepSeek-TUI-termux.git
cd DeepSeek-TUI-termux
bash build_deepseek.sh
```

Once built, copy the binaries to your PATH:

```bash
cp target/release/deepseek $PREFIX/bin/deepseek
cp target/release/deepseek-tui $PREFIX/bin/deepseek-tui
```

### Verify

```bash
deepseek --version
deepseek doctor
```

---

## Usage

Same as upstream DeepSeek TUI:

```bash
deepseek                                   # Launch interactive TUI
deepseek "explain this function"           # One-shot prompt
deepseek --model auto "fix this bug"       # Auto-select model + reasoning
deepseek --yolo                            # Auto-approve all tool actions
deepseek auth set --provider deepseek      # Save your API key
```

For full documentation, see the [upstream README](https://github.com/Hmbown/DeepSeek-TUI#usage).

---

## Syncing with Upstream

This fork tracks upstream releases. To sync manually:

```bash
git remote add upstream https://github.com/Hmbown/DeepSeek-TUI.git
git fetch upstream
git merge upstream/main
```

After merging, the files most likely to need attention are:
1. `crates/tui/Cargo.toml` — ensure `arboard` is unconditional and TLS uses `rustls`
2. `crates/tui/src/tui/clipboard.rs` — ensure no `target_os = "android"` cfg gates remain

---

## Credits

🎖 **Huge thanks to [Hmbown](https://github.com/Hmbown) and all [contributors](https://github.com/Hmbown/DeepSeek-TUI#thanks) for their outstanding open-source work.**

Upstream repository: [https://github.com/Hmbown/DeepSeek-TUI](https://github.com/Hmbown/DeepSeek-TUI)

- Thanks to [DeepSeek](https://github.com/deepseek-ai) for the models and API that power every turn
- Thanks to [DataWhale](https://github.com/datawhalechina) 🐋 for their support
- Thanks to every contributor who submitted PRs, bug reports, and docs to the upstream project

This repo makes only the minimal changes needed for Termux compatibility. All core functionality, architecture, and UI are from upstream.

---

## License

Same as upstream: [MIT](LICENSE)

> *Not affiliated with DeepSeek Inc.*
