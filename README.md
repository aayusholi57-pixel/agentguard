# AgentGuard

[![CI](https://github.com/aayusholi57-pixel/agentguard/actions/workflows/ci.yml/badge.svg)](https://github.com/aayusholi57-pixel/agentguard/actions/workflows/ci.yml)
[![Go](https://img.shields.io/badge/Go-1.24%2B-00ADD8?logo=go)](https://go.dev/)
[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)
[![Release](https://img.shields.io/github/v/release/aayusholi57-pixel/agentguard?sort=semver)](https://github.com/aayusholi57-pixel/agentguard/releases)

**AgentGuard is an offline Go security scanner for detecting agent-directed prompt injection hidden inside dependency documentation and metadata.**

It scans README files, changelogs, docstrings, package metadata, vendored dependencies, and other prose channels for suspicious instructions aimed at coding agents. It reports findings with file locations, rule IDs, severity, and optional SARIF output for CI/security tooling.

> **Security boundary:** AgentGuard analyzes text. It does not execute the content it scans.

## Why AgentGuard?

Modern software agents consume dependency documentation as context. A malicious package can hide instructions in otherwise ordinary prose and attempt to influence an agent's behavior.

AgentGuard adds a focused supply-chain security layer by looking for:

- Instructions explicitly addressed to coding agents
- Prompt-override language such as "ignore previous instructions"
- Destructive or suspicious imperatives
- Suspicious instructions occurring near agent-directed language
- Payloads embedded in dependency metadata and documentation

The scanner is deterministic, offline, and does not require an external model or API key.

## Highlights

- **Offline-first** — scan locally without sending dependency content to an external service
- **Single Go binary** — easy to build, distribute, and run in CI
- **Multi-ecosystem scanning** — npm, Python/PyPI, Go, and Cargo/Rust
- **Rule-based detection** — embedded corpus plus proximity heuristics
- **SARIF output** — integrate findings with compatible CI/security platforms
- **Baseline support** — scan only changed prose after establishing a baseline
- **Project configuration** — suppress or adjust individual rules with `.agentguard.yaml`
- **CI-friendly exit codes** — distinguish findings from command/configuration errors
- **Test fixtures** — included malicious and clean fixtures for repeatable validation

## Architecture

```text
┌─────────────────────┐
│   Project / Repo    │
│ dependencies + prose│
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│      Scanner        │
│ npm / Python / Go   │
│      / Cargo        │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│      Detector       │
│ corpus + heuristics │
│ + project overrides │
└──────────┬──────────┘
           │
       ┌───┴────┐
       ▼        ▼
   Text CLI    SARIF
       │        │
       └───┬────┘
           ▼
       CI / Review
```

Key source areas:

- `cmd/agentguard` — CLI entry point
- `internal/scan` — dependency discovery and document extraction
- `internal/detect` — detection rules, heuristics, configuration and severity handling
- `internal/report` — human-readable and SARIF reporting
- `corpus` — embedded detection payloads and corpus metadata
- `testdata` — deterministic security fixtures
- `.github/workflows` — automated build, test and release pipelines

## Requirements

- Go **1.24+**
- Git

## Installation

### Build from source

```bash
git clone https://github.com/aayusholi57-pixel/agentguard.git
cd agentguard

go mod download
go build -trimpath -o ./bin/agentguard ./cmd/agentguard
```

### Run directly

```bash
go run ./cmd/agentguard check .
```

## Quickstart

Scan the current project:

```bash
./bin/agentguard check .
```

Scan a specific ecosystem:

```bash
./bin/agentguard check . --ecosystem node
./bin/agentguard check . --ecosystem python
./bin/agentguard check . --ecosystem go
./bin/agentguard check . --ecosystem cargo
```

Generate SARIF for CI tooling:

```bash
./bin/agentguard check . \
  --format sarif \
  --output findings.sarif
```

Create and reuse a baseline:

```bash
./bin/agentguard check . --write-baseline baseline.json
./bin/agentguard check . --changed-only baseline.json --write-baseline baseline.json
```

Inspect the embedded detection corpus:

```bash
./bin/agentguard corpus
```

## Exit codes

| Code | Meaning |
|---:|---|
| `0` | Scan completed without findings at the configured severity |
| `1` | One or more findings met the configured severity threshold |
| `2` | Command, configuration, or execution error |

For report-only usage:

```bash
./bin/agentguard check . --exit-on-finding=false
```

## Configuration

Create `.agentguard.yaml` at the scan root:

```yaml
disable: []
allow: []
severity: {}
```

The configuration supports:

- `disable` — disable selected rule IDs
- `allow` — allow configured patterns
- `severity` — override rule severity

Rule IDs are case-insensitive.

## CI / Security Workflow

AgentGuard includes GitHub Actions for:

1. Dependency and module hygiene checks
2. `go vet ./...`
3. Reproducible builds
4. Race-enabled test execution
5. Detection smoke tests
6. SARIF output validation
7. Tagged releases through GoReleaser

Run the same core checks locally:

```bash
go mod tidy
go vet ./...
go test -race -count=1 ./...
go build -trimpath -o ./bin/agentguard ./cmd/agentguard
```

## Testing

The repository contains fixtures for both malicious and clean dependency content.

```bash
go test ./...
go test -race -count=1 ./...
```

Smoke-test a known malicious fixture:

```bash
./bin/agentguard check ./testdata/jqwik_fixture --no-color
```

The command is expected to return exit code `1` because the fixture intentionally contains findings.

## Scope and limitations

AgentGuard is a focused prompt-injection detector, not a complete software supply-chain security platform.

A clean result does **not** prove that a dependency is safe. It does not replace:

- vulnerability scanning
- malware analysis
- static analysis
- dependency provenance verification
- sandboxing
- runtime behavior analysis
- human security review

Heuristic findings also require review because natural-language context can produce false positives or false negatives.

## Project status

The project currently targets four dependency ecosystems:

- npm / Node.js
- Python / PyPI
- Go modules
- Cargo / Rust

The design intentionally keeps scanning local and deterministic so it can be embedded into developer workflows and CI pipelines.

## Repository

This repository is maintained under the **Aayush Oli** GitHub account:

**https://github.com/aayusholi57-pixel/agentguard**

The project identity, Go module path, documentation, examples, and automation are configured for this repository.

## License

Apache License 2.0. See [LICENSE](LICENSE).

This repository contains inherited project material; applicable original copyright and license notices are preserved.
