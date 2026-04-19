# AGENTS.md

Helix is a Kakoune/Neovim-inspired text editor written in Rust.

## Developer Commands

```bash
# Build & run (debug mode - faster than release)
cargo run

# Unit tests
cargo test --workspace

# Integration tests
cargo integration-test

# Formatting check
cargo fmt --all --check

# Linting
cargo clippy --workspace --all-targets -- -D warnings

# Documentation
cargo doc --no-deps --workspace --document-private-items

# Auto-generated docs (commands, languages)
cargo xtask docgen

# Query/theme validation
cargo xtask query-check
cargo xtask theme-check
```

## Monorepo Structure

- **17 workspace crates**: helix-core, helix-term, helix-view, helix-lsp, helix-lsp-types, helix-tui, helix-event, helix-loader, helix-dap-types, helix-dap, helix-stdx, helix-parsec, helix-vcs, xtask
- **Entry point**: helix-term
- **MSRV**: 1.90

### Architecture

- `helix-stdx`: Extensions to std library
- `helix-core`: Core editing primitives (Rope, Transaction, Selection) - functional, data structures are cloned
- `helix-view`: UI abstractions (Document, View, Surface, Rect)
- `helix-lsp`: Language Server Protocol client
- `helix-term`: Terminal UI (main application)
- `helix-tui`: TUI widgets and primitives

## Important Quirks

- **Tree-sitter grammars**: Cached in `runtime/grammars`, keyed by `languages.toml` hash. May need to regenerate after grammar updates.
- **Debug logging**: Use `log::info!`, `log::warn!`, `log::error!` with `hx -v <file>` to view logs. Use `--log <path>` to write to file, `tail -f` to watch.
- **MacOS ulimit**: Integration tests may fail with "Too many open files". Run `ulimit -n 10240` first.
- **rust-analyzer**: If using rustup, run `rustup component add rust-analyzer` (not auto-downloaded with toolchain).
- **Faster builds**: Try [mold](https://github.com/rui314/mold) linker.

## MSRV Bump (when updating Rust version)

Update these 3 files:
1. `Cargo.toml` - `workspace.package.rust-version`
2. `.github/workflows/build.yml` - `env.MSRV`
3. `rust-toolchain.toml` - `toolchain.channel`

## Testing

- Test helpers: `helix-term/tests/test/helpers.rs`
- Log level: `HELIX_LOG_LEVEL=debug cargo integration-test`