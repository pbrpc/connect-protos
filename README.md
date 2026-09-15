# connect-protos

Protobuf contracts for the capabilities every Connect service exposes, with
connect-go v2 clients and handlers generated for each: the `info` and
`diagnostics` services shared across the ecosystem, and the `admission`
messages a gateway consults services with.

## Overview

A service implements these interfaces so that operators and other services
can ask it the same questions the same way: which version it runs, and how its
dependencies are doing. The generated handlers register on a `*connect.Server`
and answer gRPC, gRPC-Web, and Connect-protocol clients alike. Nothing here
imports `google.golang.org/grpc`.

## Installation

```bash
go get github.com/pbrpc/connect-protos@latest
```

## Packages

| Package                          | Contents                                                                                     |
| -------------------------------- | -------------------------------------------------------------------------------------------- |
| `info`                           | `InfoService` messages                                                                       |
| `info/infoconnect`               | `InfoServiceClient`, `InfoServiceHandler`, `RegisterInfoServiceHandler`                      |
| `diagnostics`                    | `DiagnosticsService` messages                                                                |
| `diagnostics/diagnosticsconnect` | `DiagnosticsServiceClient`, `DiagnosticsServiceHandler`, `RegisterDiagnosticsServiceHandler` |
| `admission`                      | `AdmissionRequest`, `AdmissionResponse` messages                                             |

## Services

### InfoService

`Version` reports the running server's version.

### DiagnosticsService

`GetDiagnostics` reports each dependency the service holds, keyed by the name
the service gives it: the address it was reached at, its serving status, its
reachability, when it was last checked, and any further details.

## Admission

A gateway consults admission services before forwarding a request. The
contract is the two messages, with no shared service: each admission service
declares its own unary RPC taking `AdmissionRequest` and returning
`AdmissionResponse`, and the gateway is configured with that RPC's procedure
name. Method discovery keys on the procedure, so two admission services never
share one.

`AdmissionRequest` carries the target procedure, every header as received, and
the peer (remote address and, over TLS, the client certificate). The body is
never sent. `AdmissionResponse` names headers to remove and headers to set on
the forwarded request, `remove` applied before `set`, and may name a
different procedure to forward to. A refusal is the error the RPC returns,
which the gateway answers to the client without forwarding.

```proto
service Authenticator {
  rpc Authenticate(admission.AdmissionRequest) returns (admission.AdmissionResponse);
}
```

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
