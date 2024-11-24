# Integrato

**Integrato** is an open-source integration testing tool designed for developers who need a seamless way to test applications across various environments. With **Integrato**, you can spin up mock servers, define integration tests in YAML, and test against local, QA, or custom environments using profiles.

## Features

### Summary of Features

| **Category**             | **Feature**                                     | **Description**                                                                                                                                         | **Included in MVP** |
|---------------------------|-------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------|
| **Mock Configuration**    | Mock OpenAPI APIs                               | Dynamically generate mock servers from OpenAPI spec files.                                                                                              | ✅                   |
|                           | Mock Common Services                            | Spin up services like **MySQL**, **Kafka**, or **Redis** via Docker or local binaries.                                                                  | ✅                   |
|                           | Dynamic Mock Responses                          | Define mock responses with dynamic values like timestamps or random IDs.                                                                                | ❌                   |
|                           | Stateful Mocking                                | Simulate stateful APIs to handle workflows like login or transactions.                                                                                  | ❌                   |
| **Integration Testing**   | YAML-Defined Tests                              | Define API integration tests in YAML, including expected requests and responses.                                                                        | ✅                   |
|                           | gRPC Testing                                    | Test gRPC services with method and message validation.                                                                                                  | ❌                   |
|                           | Partial Assertions                              | Validate parts of responses or headers instead of requiring full matches.                                                                               | ✅                   |
|                           | Protocol Support                                | Extend testing to **GraphQL**, **WebSocket**, and **message queues** (Kafka).                                                                           | ❌                   |
|                           | Test Flows                                      | Sequentially chain tests where one test’s output is another’s input.                                                                                    | ✅                   |
| **Profiles**              | Local Profiles                                  | Spin up mock servers and test applications locally.                                                                                                     | ✅                   |
|                           | Remote Profiles                                 | Run tests against remote environments like QA or staging servers.                                                                                       | ✅                   |
|                           | Custom Profiles                                 | Allow users to define custom profiles with unique configurations.                                                                                       | ✅                   |
| **Execution and Reporting** | Unified CLI Command                            | Execute all tests and mocks with a single CLI command.                                                                                                  | ✅                   |
|                           | Parallel Test Execution                         | Run independent tests in parallel for faster execution.                                                                                                 | ❌                   |
|                           | Detailed Test Reports                           | Generate test reports in **HTML**, **JSON**, or **JUnit XML** formats.                                                                                   | ✅                   |
| **Non-Functional**        | Lightweight Tool                                | Ensure minimal runtime dependencies for efficient performance.                                                                                          | ✅                   |
|                           | Cross-Platform Compatibility                    | Support Linux, macOS, and Windows environments.                                                                                                         | ✅                   |
|                           | Secure Environment                              | Mask sensitive data like credentials or tokens in logs and reports.                                                                                     | ✅                   |

---

## Installation

Integrato will be distributed as a Go binary for easy installation across platforms. Precompiled binaries will be available for Linux, macOS, and Windows.

### Download
```bash
# Coming Soon: Use curl or wget to download the binary
curl -L https://github.com/kasattejaswi/integrato/releases/download/v0.1/integrato -o /usr/local/bin/integrato
chmod +x /usr/local/bin/integrato
```

---

## Usage [Not Final]

### 1. Define `.integrato.yaml`
The `.integrato.yaml` file is the core of Integrato. It defines your mocks, tests, and profiles. Example:

```yaml
profiles:
  local:
    description: "Run integration tests locally with mock servers"
    mocks:
      enabled: true
      services:
        - type: openapi
          path: ./specs/api-spec.yaml
        - type: mysql
          image: mysql:8
    application:
      start: docker
      docker_compose: ./docker-compose.yml
      env:
        - DATABASE_URL=mysql://root:password@localhost:3306/testdb
    tests:
      run_all: true

  qa:
    description: "Run integration tests against QA server"
    mocks:
      enabled: false
    application:
      endpoint: https://qa.example.com
      env:
        - AUTH_TOKEN=abcdef123456
    tests:
      exclude:
        - "tests/slow_test.yaml"
```

### 2. Run Tests
Run all tests for the local profile:
```bash
integrato run --profile=local
```

Run tests against the QA profile:
```bash
integrato run --profile=qa
```

### 3. View Reports
Generate and view detailed test reports:
```bash
integrato report --format=html
```

---

## Roadmap

### **MVP Features**
- YAML-based configuration for mocks and tests.
- OpenAPI mock server support.
- Support for spinning up common services (MySQL, Kafka).
- Local and remote profile support.
- Basic test execution with detailed reports.

### **Future Features**
- Stateful mocking.
- gRPC, GraphQL, and WebSocket protocol support.
- Parallel test execution.
- Advanced test orchestration (dependencies, conditional flows).

---

## Contributing

We welcome contributions from the community! To get started:
1. Fork the repository.
2. Create a feature branch.
3. Submit a pull request with a detailed explanation of your changes.

## License

Integrato is released under the [GNU AFFERO GENERAL PUBLIC LICENSE](LICENSE).
