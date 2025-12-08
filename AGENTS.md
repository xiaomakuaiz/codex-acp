# Repository Guidelines

## Project Structure & Module Organization
This repository hosts the Codex ACP CLI built in Rust. Core sources live in `src/`: `main.rs` wires up the CLI entrypoint, `lib.rs` exposes `run_main`, while modules such as `codex_agent.rs`, `conversation.rs`, `local_spawner.rs`, and `prompt_args.rs` isolate protocol, state, and CLI parsing concerns. The `npm/` directory wraps the binary for npm distribution and stores packaging scripts. Release signing scripts reside in `script/`. CI workflows live in `.github/workflows/`; match their expectations locally before pushing.

## Build, Test, and Development Commands
- `cargo check` quickly validates the workspace and catches type errors.
- `cargo fmt --all` formats source with `rustfmt`; CI enforces `-- --check`.
- `cargo clippy --all-targets --all-features -- -D warnings` should be warning-free before review.
- `cargo test` (or `cargo test --release` to align with CI) runs the Rust test suite.
- `bash npm/testing/validate.sh` verifies the npm wrapper, template placeholders, and version sync.
- `node npm/testing/test-platform-detection.js` exercises npm platform detection logic.

## Coding Style & Naming Conventions
Code targets Rust 2024; prefer idiomatic patterns, four-space indentation, and module-private helpers unless reuse is intended. Follow `snake_case` for functions, methods, and locals; `CamelCase` for types and traits; `SCREAMING_SNAKE_CASE` for constants. Keep asynchronous sections `Send`-safe when possible and lean on `LocalSet` only where necessary. Avoid `println!`; tracing output already flows through `tracing_subscriber`. Run `cargo fmt` and `cargo clippy` before sending changes.

## Testing Guidelines
New behaviour should be covered with unit tests colocated in `#[cfg(test)]` modules inside the relevant file, using `tokio::test` for async paths. Keep assertions deterministic and avoid relying on networked services. Ensure npm packaging scripts keep passing `npm/testing/validate.sh`, and update smoke tests when packaging metadata changes.

## Commit & Pull Request Guidelines
Commits follow concise, present-tense summaries similar to `prep 0.6`; keep them under 72 characters and explain context in the body when needed. Reference issue IDs in the body (`Fixes #123`) instead of the subject. Pull requests should describe the change, ideally link to tracking issues, enumerate validation commands run, and call out impacts on release artifacts or configuration. Attach logs or screenshots when updating CLI output or workflows.

## Release & Packaging Notes
When preparing a release, confirm `Cargo.toml` and `npm/package.json` versions match, regenerate platform packages via `npm/publish/create-platform-packages.sh`, and use the provided signing scripts for macOS and Windows binaries. Never commit secrets—pass authentication via environment variables such as `OPENAI_API_KEY` or `CODEX_API_KEY`.
