# Reflex Protocol Implementation

## Overview

This repository contains an implementation of the Reflex protocol integrated with Xray-Core.

The project is based on the original Reflex protocol project and focuses on implementing the protocol components required for secure communication, traffic obfuscation, frame processing, replay protection, and integration with the Xray-Core architecture.

The original project is available at:

https://github.com/soroushdeimi/reflex

This repository contains the implementation and testing work developed on top of that project.

---

## Contributors

| Name               | Student ID | GitHub                                                          |
| ------------------ | ---------: | --------------------------------------------------------------- |
| Mehrshad Haghighat |  402100418 | [MehrshadHaghighat007](https://github.com/MehrshadHaghighat007) |
| Mobin Yousefi      |  402100594 | [TheRoshangar](https://github.com/TheRoshangar)                 |

---

## Project Background

Reflex is a proxy protocol designed to provide secure communication while reducing the ability of network observers and Deep Packet Inspection (DPI) systems to identify proxy traffic through simple traffic characteristics.

The implementation in this repository integrates the Reflex protocol into the Xray-Core codebase.

The main objectives of the implementation are:

* Implementing the Reflex protocol within the Xray-Core architecture.
* Providing authenticated and encrypted communication.
* Implementing secure frame processing.
* Providing replay protection.
* Applying traffic-morphing techniques.
* Testing the implementation under different conditions.
* Evaluating security and performance characteristics.

---

## Architecture

The implementation is integrated into the Xray-Core networking architecture rather than being implemented as an independent proxy application.

At a high level, the communication process consists of the following components:

```text
Client
  |
  | Reflex Protocol
  v
Reflex Session
  |
  | Encrypted Frames
  v
Network
  |
  v
Reflex Session
  |
  v
Xray-Core
  |
  v
Destination
```

The Reflex layer is responsible for processing protocol-specific frames and applying the required encryption, validation, and traffic-morphing mechanisms.

---

## Main Features

### Authenticated Encryption

The implementation uses ChaCha20-Poly1305 for authenticated encryption.

This provides both confidentiality and integrity for protected protocol frames.

The use of an authenticated encryption scheme allows the receiver to verify that encrypted data has not been modified during transmission.

---

### Frame Processing

Communication is organized into protocol frames.

Frame processing is responsible for:

* Constructing protocol frames.
* Parsing received frames.
* Validating frame contents.
* Encrypting outgoing frame data.
* Decrypting incoming frame data.
* Rejecting invalid or malformed frames.

This separation allows the protocol implementation to remain independent from the underlying transport mechanism.

---

### Replay Protection

Replay protection is implemented to prevent previously accepted protocol frames from being reused by an attacker.

The implementation uses nonce-related validation and frame sequencing to ensure that previously processed frames cannot simply be submitted again as valid communication.

Replay-related behavior is also included in the security testing of the implementation.

---

### Traffic Morphing

The protocol includes traffic-morphing mechanisms intended to make Reflex traffic less distinguishable from ordinary network traffic.

The implementation considers characteristics such as:

* Packet sizes.
* Frame sizes.
* Traffic patterns.
* Timing behavior.

The purpose of these mechanisms is to reduce simple protocol fingerprinting based solely on observable traffic characteristics.

---

### Secure Validation

Protocol input is validated before being accepted by the implementation.

The validation process is designed to reject malformed or invalid frames and to prevent invalid state transitions.

Security-sensitive comparisons are handled using appropriate comparison mechanisms where required.

---

## Implementation

The implementation is located within the Xray-Core component of the repository.

The repository follows the general structure of Xray-Core while introducing the components required for the Reflex protocol.

The implementation is designed to interact with the existing Xray-Core networking and connection abstractions rather than introducing an unrelated networking stack.

This approach allows the Reflex protocol to operate as part of the existing Xray-Core architecture.

---

## Testing

Testing is an important part of the project and covers different aspects of the implementation.

The test suite includes functional, integration, security, fuzzing, and performance-oriented tests.

### Functional Testing

Functional tests verify that individual components behave according to their expected behavior.

These tests cover areas such as:

* Frame creation.
* Frame parsing.
* Encryption.
* Decryption.
* Protocol validation.
* Error handling.

---

### Integration Testing

Integration tests verify the interaction between the Reflex implementation and the networking abstractions used by Xray-Core.

The tests use controlled connections to simulate communication between protocol endpoints without requiring a complete external network environment.

A `FakeConn` abstraction is used where necessary to provide the connection behavior required by the Xray-Core interfaces during testing.

---

### Security Testing

Security tests evaluate behavior under invalid or malicious conditions.

The security-oriented tests include scenarios involving:

* Invalid frames.
* Modified encrypted data.
* Invalid authentication data.
* Replay attempts.
* Incorrect nonce values.
* Concurrent access.
* Invalid protocol state.

The purpose of these tests is to ensure that invalid input is rejected safely and that protocol state cannot be manipulated through simple malformed requests.

---

### Fuzz Testing

Fuzz testing is used to evaluate the robustness of frame parsing and processing when receiving unexpected or randomly generated input.

For example:

```bash
go test -v -fuzz=FuzzReadFrame -fuzztime=30s ./tests/security_fuzz_test.go
```

Fuzz testing is particularly useful for protocol implementations because network input cannot be assumed to be correctly formatted.

---

### Performance Testing

Performance benchmarks are included to evaluate the computational cost of the implementation.

The benchmark suite can be executed using:

```bash
go test -v -bench=. -benchmem ./tests/performance_test.go
```

The benchmarks provide information about execution time and memory allocation and can be used to compare changes made to the implementation.

---

## Development Challenges

Several implementation challenges were addressed during the development of the project.

### Integration with Existing Xray-Core Interfaces

The Reflex implementation needs to work with the existing Xray-Core connection and networking abstractions.

This required adapting protocol-specific functionality to the interfaces already used by the Xray-Core architecture.

---

### Testing Network Connections

Some protocol behavior depends on network connection interfaces that are not directly convenient to reproduce in isolated unit tests.

To address this issue, a controlled connection abstraction was introduced for testing purposes.

This allows protocol behavior to be tested without requiring a complete external network environment.

---

### Concurrent Execution

Network protocols are expected to operate correctly under concurrent execution.

Therefore, concurrency-related behavior was considered during testing, including situations where multiple operations may access shared protocol state.

Race detection and concurrent test scenarios are useful for identifying synchronization problems that may not appear during ordinary sequential testing.

---

### Replay and Nonce Handling

Correct nonce management is important when using authenticated encryption.

The implementation therefore treats nonce generation and validation as part of the protocol state rather than treating encrypted frames as independent messages without sequencing.

Testing was performed to verify that invalid or repeated protocol states are rejected appropriately.

---

## Repository Structure

The repository follows the structure of the original project and Xray-Core.

A simplified representation of the repository is:

```text
.
├── .github/
├── docs/
├── xray-core/
├── config.example.json
├── .gitignore
└── README.md
```

The main protocol implementation and its associated tests are contained within the Xray-Core portion of the repository.

---

## Requirements

The project requires a Go development environment compatible with the version used by the Xray-Core source tree.

Before building the project, verify that Go is installed:

```bash
go version
```

Git is also required to obtain and manage the repository.

---

## Getting Started

Clone the repository:

```bash
git clone https://github.com/MehrshadHaghighat007/reflex.git
```

Enter the repository:

```bash
cd reflex
```

If the implementation is located on the specified development branch:

```bash
git checkout mobin-mehrshad-402100594-402100418
```

The project can then be built and tested using the Go toolchain.

---

## Running Tests

To run the available tests:

```bash
go test -v ./tests/...
```

For performance benchmarks:

```bash
go test -v -bench=. -benchmem ./tests/performance_test.go
```

For fuzz testing:

```bash
go test -v -fuzz=FuzzReadFrame -fuzztime=30s ./tests/security_fuzz_test.go
```

When investigating concurrency-related issues, the Go race detector can also be used where applicable:

```bash
go test -race ./...
```

---

## Configuration

An example configuration is provided in:

```text
config.example.json
```

The configuration should be adapted to the environment in which the implementation is being tested.

Sensitive information such as private keys, credentials, or environment-specific configuration should not be committed to the repository.

---

## Security Considerations

This project is primarily an implementation and research project.

Although the implementation includes authenticated encryption, replay protection, input validation, fuzz testing, and security-oriented tests, the presence of these mechanisms does not constitute a formal security proof or independent security audit.

A production deployment of security-sensitive protocol software should undergo additional review, including:

* Cryptographic review.
* Protocol security analysis.
* Independent code review.
* Adversarial testing.
* Interoperability testing.
* Long-term performance and reliability testing.

---

## Relationship to the Original Project

This repository is based on the original Reflex project:

https://github.com/soroushdeimi/reflex

The original repository provides the project specification and foundation for the implementation.

The purpose of this repository is to provide our implementation and associated testing work within the Xray-Core environment.

---

## References

### Original Reflex Repository

https://github.com/soroushdeimi/reflex

### Xray-Core

https://github.com/XTLS/Xray-core

### Go Programming Language

https://go.dev/

---

## Authors

### Mehrshad Haghighat

Student ID: 402100418

GitHub: https://github.com/MehrshadHaghighat007

### Mobin Yousefi

Student ID: 402100594

GitHub: https://github.com/TheRoshangar

---

## License

Please refer to the license of the original project and the corresponding Xray-Core components for applicable licensing terms.
