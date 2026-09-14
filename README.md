# grpc-protos-connect

Protobuf interface definitions for common `connect` service capabilities.
Provides standard service contracts that microservices can implement for
observability and service discovery.

## Overview

This library contains protobuf definitions for cross-cutting service concerns.
Services implementing these interfaces provide consistent APIs for querying
service information and diagnostic state across a microservices ecosystem.

## Installation

```bash
go get git.sonicoriginal.software/grpc-protos/info@latest
go get git.sonicoriginal.software/grpc-protos/diagnostics@latest
```

## Services

### InfoService

Provides service version and metadata information. Services implementing this
interface expose their version number and additional key-value details about
their configuration or runtime state.

Useful for service inventories, version tracking, and debugging deployment
issues.

### DiagnosticsService

Exposes diagnostic information about a service's dependencies. Services
implementing this interface report the connection state, serving status, and
health of external services they depend on.

Includes connection addresses, last check timestamps, and flexible key-value
diagnostic details. Enables debugging connectivity issues and understanding
service dependency health.

## Generated Code

This repository uses automated proto generation. The default branch contains
protobuf source files, while the `gen` branch contains generated Go code.
Releases are tagged with timestamp-based versions.

Go modules automatically resolve to the `gen` branch when importing packages.

## Usage

Import the generated protobuf packages into your service and implement the
service interfaces. Register the implementations with your gRPC server to expose
these capabilities.

## Requirements

- Go 1.21 or later
- gRPC Go libraries
- Protocol Buffers runtime

## License

Apache License 2.0
