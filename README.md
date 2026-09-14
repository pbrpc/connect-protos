# connect-protos

Protobuf contracts for the capabilities every Connect service exposes, with
connect-go v2 clients and handlers generated for each: the standard
`grpc.health.v1` health service, and the `info` and `diagnostics` services
shared across the ecosystem.

## Overview

A service implements these interfaces so that probes, operators, and other
services can ask it the same questions the same way: whether it is serving,
which version it runs, and how its dependencies are doing. The generated
handlers register on a `*connect.Server` and answer gRPC, gRPC-Web, and
Connect-protocol clients alike. Nothing here imports `google.golang.org/grpc`.

## Installation

```bash
go get github.com/pbrpc/connect-protos@latest
```

## Packages

| Package                          | Contents                                                                                     |
| -------------------------------- | -------------------------------------------------------------------------------------------- |
| `health`                         | `grpc.health.v1` messages, generated from the canonical `grpc/health/v1/health.proto`        |
| `health/healthconnect`           | `HealthClient`, `HealthHandler`, `RegisterHealthHandler`                                     |
| `info`                           | `InfoService` messages                                                                       |
| `info/infoconnect`               | `InfoServiceClient`, `InfoServiceHandler`, `RegisterInfoServiceHandler`                      |
| `diagnostics`                    | `DiagnosticsService` messages                                                                |
| `diagnostics/diagnosticsconnect` | `DiagnosticsServiceClient`, `DiagnosticsServiceHandler`, `RegisterDiagnosticsServiceHandler` |

## Services

### grpc.health.v1.Health

The GRPC Health Checking Protocol: `Check` for one service's status, `Watch` for
a stream of its changes, `List` for every recorded service. The `""` service
names the process. This is the interface Kubernetes gRPC probes,
`grpc_health_probe`, and proxies' health checkers speak.

The proto is not copied here. `buf.yaml` depends on the `buf.build/grpc/grpc`
module, and `buf.gen.yaml` names its `grpc/health/v1/health.proto` as an input
with the Go package rewritten to this module, so the descriptor is the canonical
one. A binary that also links `google.golang.org/grpc/health/grpc_health_v1`
registers the same descriptors twice and panics at init; a Connect binary
imports this package and a grpc-go binary imports that one.

### InfoService

`Version` reports the running server's version.

### DiagnosticsService

`GetDiagnostics` reports each dependency the service holds, keyed by the name
the service gives it: the address it was reached at, its serving status, its
reachability, when it was last checked, and any further details.

## Generated Code

The default branch holds the protobuf sources. The `gen` branch holds the Go
code that `buf generate` produces from them, and Go modules resolve to it when
importing this module. Releases are tagged from it.

`protoc-gen-connect-go` runs as a local plugin, so generating requires the
connect-go v2 generator on the path:

```bash
go install connectrpc.com/connect/v2/cmd/protoc-gen-connect-go@latest
```

## Usage

Register the generated handlers on a `*connect.Server` alongside your own, then
mount the server with `connecthttp.Mount`.
