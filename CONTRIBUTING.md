# Contributing to AgentGuard

Thank you for helping improve AgentGuard.

## Development setup

Requirements:

- Go 1.24+
- Git

Clone the repository and run:

```bash
go mod download
go test ./...
go vet ./...
go build ./cmd/agentguard
```

For the full CI-equivalent test suite:

```bash
go test -race -count=1 ./...
```

## Pull requests

Please keep pull requests focused and include:

- A clear description of the change
- Tests for behavior changes
- Documentation updates when the CLI or configuration changes
- Evidence that `go test ./...` and `go vet ./...` pass

Security-sensitive changes should include regression fixtures whenever practical.

## Code style

Use standard Go formatting:

```bash
gofmt -w .
```

Prefer small, testable packages and deterministic behavior. Avoid introducing network access into the scanner unless the design explicitly requires it.

## Commit guidance

Use concise commit messages that explain the change, for example:

- `feat: add new detection rule`
- `fix: handle malformed package metadata`
- `test: add regression fixture`
- `docs: improve CLI usage`

## Security

Do not disclose a suspected vulnerability in a public issue. Follow [SECURITY.md](SECURITY.md).
