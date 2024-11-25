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


## Command Overview

```
Integrato: Simplify integration testing with profiles, mocks, and Docker.
Usage:
  integrato [command] [options]

Commands:
  init                 Initialize a new .integrato.yaml file in the current directory.
  run                  Execute integration tests based on the specified profile.
  mocks                Manage mock servers (start, stop, list).
  profiles             List available profiles and their configurations.
  report               Generate and view test reports.
  cleanup              Stop and remove all mock servers and related containers.

Use "integrato [command] --help" for more information about a command.
```

---

## Commands

### 1. `integrato init`

```
$ integrato init
```

- **Description**:  
  Initialize a new `.integrato.yaml` configuration file in the current directory.

- **Options**:  
  - `--overwrite`: Overwrite an existing `.integrato.yaml` file if present.

- **Example**:  
  ```
  $ integrato init
  ✔ Initialized .integrato.yaml in the current directory.
  ```

---

### 2. `integrato run`

```
$ integrato run --help
```

- **Description**:  
  Run integration tests for the specified profile. Spins up mock servers, starts the application, and executes tests.

- **Options**:  
  - `--profile <name>`: Specify the profile to use (Required).  
  - `--parallel`: Run independent tests in parallel (Optional).  
  - `--verbose`: Enable detailed output during test execution (Optional).  

- **Example**:  
  ```
  $ integrato run --profile=local --verbose
  ✔ Mock servers started for profile: local.
  ✔ Application started locally.
  ✔ Running integration tests...
    - Test 1: User Login .......... PASS
    - Test 2: API Response Check .. FAIL
      Reason: Expected status 200 but received 500.
    - Test 3: Kafka Topic Publish . PASS
  ✔ Tests completed: 2 passed, 1 failed.
  ✔ Report generated: ./reports/integrato-report.html
  ```

---

### 3. `integrato mocks`

```
$ integrato mocks --help
```

- **Description**:  
  Manage mock servers independently. Useful for debugging or manual testing.

- **Subcommands**:  
  - `start`: Start mock servers for a given profile.  
  - `stop`: Stop mock servers for a given profile.  
  - `list`: List active mock servers and their details.  

- **Options**:  
  - `--profile <name>`: Specify which profile’s mocks to start or stop.

- **Examples**:  
  ```
  $ integrato mocks start --profile=local
  ✔ Mock servers started for profile: local.
    - OpenAPI Mock: http://localhost:4000
    - MySQL Server: Running on port 3306

  $ integrato mocks list
  ✔ Active mock servers:
    - OpenAPI Mock: http://localhost:4000
    - MySQL Server: Running on port 3306

  $ integrato mocks stop --profile=local
  ✔ Stopped mock servers for profile: local.
  ```

---

### 4. `integrato profiles`

```
$ integrato profiles --help
```

- **Description**:  
  List available profiles from the `.integrato.yaml` file.

- **Example**:  
  ```
  $ integrato profiles list
  ✔ Available profiles:
    - local: Mocks enabled, application starts via Docker.
    - qa: Mocks disabled, testing against https://qa.example.com.
    - staging: Mocks disabled, application uses preproduction services.
  ```

---

### 5. `integrato report`

```
$ integrato report --help
```

- **Description**:  
  Generate and display test reports.

- **Options**:  
  - `--format <type>`: Specify the format for the report: `html`, `json`, `junit` (Required).  
  - `--output <path>`: Specify the output path for the report (Optional).  

- **Examples**:  
  ```
  $ integrato report --format=html --output=./reports/test-report.html
  ✔ Report generated: ./reports/test-report.html

  $ integrato report --format=json
  ✔ Report generated: ./reports/integrato-report.json
  ```

---

### 6. `integrato cleanup`

```
$ integrato cleanup --help
```

- **Description**:  
  Stops all running containers created by Integrato and removes related resources.

- **Options**:  
  - `--all`: Stop and remove all containers across profiles.

- **Example**:  
  ```
  $ integrato cleanup
  ✔ Stopped 2 mock servers.
  ✔ Removed 2 Docker containers.
  ✔ Cleanup complete.
  ```

---

## General Notes

- **Error Messages**:  
  ```
  $ integrato run --profile=nonexistent
  ✖ Profile "nonexistent" not found in .integrato.yaml.
  ```

- **Docker-Specific Errors**:  
  ```
  $ integrato run --profile=local
  ✖ Docker daemon is not running. Please start Docker and retry.
  ```

## Comparison Table

| **Feature/Aspect**          | **Integrato**                                                                                     | **Cucumber**                                                                               | **WireMock**                                                                         | **MockServer**                                                                       | **Postman/Newman**                                                                   |
|-----------------------------|---------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
| **Core Purpose**            | Unified tool for integration testing, mocking, and multi-environment profiling.                  | Behavior-driven development with test scenarios.                                          | API mocking and simulation for HTTP/HTTPS.                                          | HTTP/HTTPS mocking with request validation.                                         | API testing and automation with collections.                                         |
| **Mocking Support**         | Built-in support for REST, gRPC, OpenAPI, Kafka, databases, and more.                            | No native mocking; requires external tools.                                               | Focuses solely on HTTP/HTTPS APIs.                                                  | Focuses solely on HTTP/HTTPS APIs.                                                  | Limited mocking via Postman servers.                                                |
| **Test Configuration**      | YAML-based configuration for tests, mocks, and profiles.                                         | Gherkin-based; requires code for step definitions.                                        | No built-in test execution; requires external test frameworks.                      | No built-in test execution; requires external test frameworks.                      | Requires Postman collections and manual or scripted execution.                      |
| **Multi-Protocol Support**  | REST, gRPC, GraphQL, WebSocket, Kafka, and more.                                                 | HTTP/REST only.                                                                           | HTTP/REST only.                                                                     | HTTP/REST only.                                                                     | HTTP/REST only.                                                                     |
| **Environment Profiles**    | Native support for `local`, `QA`, `staging`, and custom profiles for mocks and tests.            | Requires custom scripts or CI/CD tools for environment management.                       | No built-in support for environment profiling.                                       | No built-in support for environment profiling.                                       | Requires manual setup or CI integration for different environments.                 |
| **Dynamic Mocking**         | Allows stateful or dynamic responses (e.g., timestamps, request-based).                          | Not supported.                                                                            | Basic request matching; lacks dynamic/stateful responses.                           | Basic request matching; lacks dynamic/stateful responses.                           | Limited to static mock responses.                                                   |
| **Ease of Use**             | Single YAML file for configuration; no additional code needed.                                   | Gherkin is readable but requires coding step definitions.                                 | Requires configuration files and separate orchestration for tests.                  | Requires configuration files and separate orchestration for tests.                  | GUI-driven; CLI (Newman) requires additional scripting.                              |
| **CI/CD Integration**       | CLI-based, integrates seamlessly into pipelines; generates detailed reports.                     | Integrates with CI but requires test runners and step definitions.                       | Requires additional tools for CI integration.                                       | Requires additional tools for CI integration.                                       | CLI available (Newman) but requires manual setup.                                   |
| **Supported Outputs**       | HTML, JSON, JUnit XML for reports.                                                               | Relies on test runners for output generation.                                             | No built-in reporting; requires external tools.                                     | No built-in reporting; requires external tools.                                     | JSON or console output via Newman.                                                  |
| **Profiles for Multi-Env**  | Predefined profiles for `local`, `QA`, and custom configurations.                               | No built-in multi-environment profiling; manual scripting needed.                        | No environment profiling support.                                                   | No environment profiling support.                                                   | No environment profiling support; manual changes required for different setups.     |
| **Lightweight Dependency**  | Uses Docker; no heavy frameworks required.                                                      | Requires specific runtime environments for step execution.                               | Lightweight but limited to HTTP/HTTPS.                                              | Lightweight but limited to HTTP/HTTPS.                                              | Requires Postman app for creation and collections; CLI (Newman) is lighter.         |
| **Unique Features**         | Unified YAML for mocks, tests, and profiles; multi-protocol support; Docker-first approach.     | Human-readable test scenarios; widely adopted for BDD.                                   | Advanced HTTP mocking and simulation features.                                       | Strong validation for HTTP/HTTPS requests and responses.                            | Comprehensive API testing GUI; integration with collections.                        |

---

## Key Advantages of Integrato

1. **Unified Tool**:
   - Combines mocking, integration testing, and environment profiling in a single CLI-driven workflow.
   - Removes the need to juggle multiple tools for testing and mocking.

2. **Multi-Protocol Support**:
   - Supports REST, gRPC, GraphQL, WebSocket, Kafka, and databases.
   - Competitors like WireMock and MockServer are limited to HTTP/HTTPS.

3. **YAML-Driven Simplicity**:
   - A single `.integrato.yaml` file defines mocks, tests, and profiles.
   - Eliminates the need for scripting or additional code like Cucumber’s step definitions.

4. **Profiles for Multi-Environment Testing**:
   - Native support for local, QA, staging, and custom profiles simplifies switching between environments.

5. **Lightweight and Portable**:
   - Uses Docker for all services, making it easy to run anywhere without heavy dependencies.

6. **Developer and CI-Friendly**:
   - CLI-first design integrates seamlessly into CI/CD pipelines.
   - Generates detailed reports in multiple formats (HTML, JSON, JUnit).

---

## Why Integrato Stands Out

Existing tools like Cucumber, WireMock, and MockServer focus on specific testing or mocking capabilities but lack the cohesive experience offered by Integrato. Integrato fills gaps by:

1. **Combining Testing and Mocking**:
   - Unlike Postman/Newman, which is API-focused, Integrato handles both mocking and testing.

2. **Simplifying Workflows**:
   - YAML-based workflows reduce complexity compared to Gherkin or manual configuration files.

3. **Supporting Advanced Use Cases**:
   - Offers support for diverse protocols and stateful responses, surpassing HTTP-only tools like MockServer.

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
