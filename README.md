# digiRocket (dgrkt)

**digiRocket (dgrkt)** is a lightweight yet powerful **command-line orchestration tool** built with Go and Cobra.  
It helps developers **automate, organize, and monitor multi-command workflows** through a simple declarative YAML configuration.

Instead of juggling multiple terminal windows or writing complex shell scripts, dgrkt allows you to:  
- Define all tasks in one YAML file.  
- Manage dependencies between processes.  
- Run everything with a **single command**.  

Whether you are setting up **local development environments**, running **test suites**, or managing **automation pipelines**, digiRocket makes it easier, more reliable, and less error-prone.

## Why digiRocket?
- 🚀 **Simplify complexity** – Stop remembering long command chains. Manage everything in YAML.  
- ✅ **Reliable execution** – Built-in health checks ensure tasks only run when dependencies are ready.  
- 🔄 **Flexible orchestration** – Run tasks concurrently or sequentially as your workflow demands.  
- 🛠️ **Developer-friendly** – Ideal for repetitive setups in dev, test, or CI/CD environments.  
- 🔍 **Transparent and observable** – Easy to debug, verify, and reproduce environments.  

---

## Typical Use Cases
- Spin up **local development environments** with multiple services (databases, API servers, message queues).  
- Run **integration tests** that require dependent services to be alive before execution.  
- Automate **CI/CD workflows** with reproducible, declarative task orchestration.  
- Replace fragile **bash scripts or Makefiles** with structured, maintainable YAML configs.  

---

## Quick Start

Here’s a minimal example:

**`dgrkt.yml`**
```yaml
tasks:
  api:
    executable: go
    args: ["run", "./cmd/api"]
    healthcheck:
      http:
        url: http://localhost:8080/health

  worker:
    executable: go
    args: ["run", "./cmd/worker"]
    depends_on: [api]
```

Run with:
```bash
dgrkt up
```

👉 dgrkt will start the **API service**, wait until it passes its health check, and only then start the **worker**.

---

## Features
- **Declarative Configuration**: Define all your commands and their settings in a human-readable YAML file.  
- **Process Orchestration**: Run multiple commands concurrently or sequentially based on defined dependencies.  
- **Comprehensive Health Checks**: Ensure your tasks are healthy before proceeding.  
- **HTTP Health Checks**: Verify service availability via HTTP GET requests.  
- **JSON Path Validation**: Extract and validate specific values from JSON HTTP responses.  
- **Command Health Checks**: Use custom shell commands to determine task health.  
- **Dependency Management**: Specify task dependencies to control the startup order.  
- **Flexible Execution**: Set `base_dir`, `executable`, `args`, and `envs` for each task.  

---

## Comparison to Alternatives
- **vs Shell Scripts**: YAML is cleaner, more maintainable, and supports health checks + dependency logic.  
- **vs Makefiles**: Designed for orchestration, not just build targets.  
- **vs Docker Compose**: Works for any executable, not limited to containers.  



# $\color{Red}\Huge{\textsf{Important Note!}}$

Because we have $\color{Red}\large{\textbf{NOT}}$ purchased Apple and Microsoft developer accounts,
you'll currently need to manually bypass system security blocks when running the application.


## Installation

### Using Package Manager

#### Homebrew (macos/linux)

- [homebrew installation](https://brew.sh/)

```shell
brew install --cask TPIsoftwareOSPO/tap/dgrkt

# macos (as "Important Note!" mention above)
xattr -dr com.apple.quarantine $(which dgrkt)
```

#### Scoop (windows)

- [scoop installation](https://scoop.sh/)

```shell
# add scoop tap
scoop bucket add TPIsoftwareOSPO https://github.com/TPIsoftwareOSPO/scoop-bucket.git
# install dgrkt
scoop install dgrkt
```

### Portable Executable

Download the compressed file that matches your system from [release](https://github.com/TPIsoftwareOSPO/digiRocket/releases).
The filename prefix will be `dgrkt-portable_`.

To run the portable executable, it must be in the same directory with the `dgrkt.yaml` file.

### Basics

`dgrkt.yaml`

```yaml
tasks:
  - name: echo
    executable: echo
    args:
      - hello
      - world
```

To execute tasks defined in your configuration file:

```bash
dgrkt up
```

output:
```shell
dgrkt|Using config file: ...(ellipsis)/dgrkt.yaml
echo|hello world
echo|Completed
```

### Using Docker

```shell
docker run --rm -v ./dgrkt.yaml:/app/dgrkt.yaml tpisoftwareopensource/dgrkt up
```

### Command Line Interface (CLI)

`dgrkt` provides a straightforward command-line interface.

**Usage:**

```bash
dgrkt [command]
```

**Available Commands:**

| Command    | Descriptions                                                |
|:-----------|:------------------------------------------------------------|
 | check      | Confirm the correctness of the YAML content format.         |
 | completion | Generate the autocompletion script for the specified shell. |
 | down       | Kill previous tasks processes.                              |
 | help       | Help about any command.                                     |
 | up         | Execute tasks according to the YAML configuration file.     |
 | version    | Show version number and build details of dgrkt.             |
| init       | Generate minimal dgrkt.yaml file                            |


### Configuration File Reference

The `tasks` key at the root of your YAML file contains a list of individual task definitions. Each task can have the following properties:


| key                                     | type               | description                                                                                                                                                      |
|:----------------------------------------|:-------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                                  | string, required   | A unique identifier for the task.                                                                                                                                |
| `base_dir`                              | string             | The working directory for the command. cmd.Dir will be set to this path. If not specified, the current working directory of dgrkt will be used.                  |
| `executable`                            | string, required   | The path to the executable command                                                                                                                               |e.g., node, java, ./my-app.|
| `args`                                  | []string           | A list of arguments to pass to the executable.                                                                                                                   |
| `envs`                                  | []string           | A list of environment variables to set for the command                                                                                                           |e.g., KEY=VALUE. These are merged with the parent process's environment variables.|
| `depends_on`                            | []string           | A list of task names that this task depends on. This task will only start after all its dependencies have successfully passed their health checks.               |
| `healthcheck`                           | object             | Defines how dgrkt determines if a task is healthy.                                                                                                               |
| `healthcheck.http`                      | object             | Configures an HTTP GET health check.                                                                                                                             |
| `healthcheck.http.url`                  | string, required   | The URL to send the HTTP GET request to.                                                                                                                         |
| `healthcheck.http.expect`               | object, optional   | Defines expected responses.If not set, a 2xx HTTP status code indicates health.                                                                                  |
| `healthcheck.http.expect.json`          | object             | Expects a JSON response.                                                                                                                                         |
| `healthcheck.http.expect.json.jsonpath` | string, required   | A JSONPath expression to extract a value from the response.                                                                                                      |
| `healthcheck.http.expect.json.value`    | string, required   | The expected value                                                                                                                                               |as a string to match against the extracted JSONPath value.|
| `healthcheck.command`                   | object             | Configures a command-based health check.                                                                                                                         |
| `healthcheck.command.scripts`           | []string, required | A list where the first element is the command, and subsequent elements are its arguments. The command is considered healthy if it exits with a zero status code. |
| `healthcheck.frequency`                 | object             | Controls the timing of health checks.                                                                                                                            |
| `healthcheck.frequency.interval`        | duration string    | The time between consecutive health check attempts                                                                                                               |e.g., 5s, 1m.|
| `healthcheck.frequency.timeout`         | duration string    | The maximum time allowed for a single health check attempt                                                                                                       |e.g., 10s.|
| `healthcheck.frequency.retries`         | int                | The maximum number of consecutive failed health checks before the task is considered unhealthy.                                                                  |
| `healthcheck.frequency.delay`           | duration string    | The initial delay before the first health check attempt is made after a task starts                                                                              |e.g., 5s.|

### Logging

The application's startup logs will be located in the `logs/` directory. 

Each log file will be named in the format `{task:name}-{date}.log`

### Examples

#### Example 1: Java Application Portable Launch Package

Here's an example of how you might configure `dgrkt` to start .jar file using java command

**Prerequisite**
1. prepare your `myapp.jar` file.
2. (Optional) prepare portable jre. (e.g [Azul Zulu](https://www.azul.com/downloads/#zulu))
3. create a new directory, put in your `myapp.jar` and jre directory. The file structure as below:

```
new-dir/
├─ jre/
    ├─ bin/
        ├─ java(.exe)
├─ myapp.jar
├─ dgrkt.yaml
├─ (Optional) dgrkt-portable(.exe)
```

**Example of dgrkt.yaml**

- With Portable Jre Java
    ```yaml
    tasks:
      - name: myapp
        base_dir: .
        executable: jre/bin/java(.exe) # portable jre executable
        args:
          - -jar
          - myapp.jar
    ```

- Use System Jre Java
    ```yaml
    tasks:
      - name: myapp
        base_dir: .
        executable: java # call system java command
        args:
          - -jar
          - myapp.jar
    ```

Double-click the dgrkt portable executable or run `dgrkt up` in your current directory's terminal.

#### Example 2: Portable Elasticsearch and Kibana Set

Here's an example of how you might configure `dgrkt` to start Elasticsearch and Kibana, 
with proper health checks and dependencies:

**Prerequisite**

1. download [elasticsearch](https://www.elastic.co/downloads/elasticsearch)
2. download [kibana](https://www.elastic.co/downloads/kibana)
3. Unzip the downloaded files and place them in the same directory. The file structure as below:

files structure:
```
new-dir/
├─ elasticsearch/
├─ kibana/
├─ dgrkt.yaml
├─ kibana.yml
```

**Example of kibana.yaml**

```yaml
csp.strict: false
server.ssl.enabled: false
telemetry:
  enabled: false
xpack:
  encryptedSavedObjects:
    encryptionKey: "01234567890123456789012345678901" # only for demo
```

**Example of dgrkt.yaml**

```yaml
tasks:
  - name: elasticsearch
    base_dir: ./elasticsearch
    executable: bin/elasticsearch
    args:
      - -E
      - xpack.security.enabled=false
      - -E
      - xpack.security.http.ssl.enabled=false
      - -E
      - xpack.security.transport.ssl.enabled=false
      - -E
      - xpack.monitoring.collection.enabled=true
    healthcheck:
      frequency:
        interval: 5s
        timeout: 10s
        retries: 5
        delay: 5s
      http:
        url: http://localhost:9200
  - name: kibana
    base_dir: ./kibana
    executable: bin/kibana
    args:
      - -c
      - kibana.yml
    healthcheck:
      frequency:
        interval: 5s
        timeout: 10s
        retries: 5
        delay: 5s
      http:
        url: http://localhost:5601/api/status
        expect:
          json:
            value: available
            jsonpath: "$.status.overall.level"
    depends_on:
      - elasticsearch
  - name: curl1
    executable: curl
    args:
      - -v
      - http://localhost:9200
    depends_on:
      - elasticsearch
  - name: curl2
    executable: curl
    args:
      - -v
      - http://localhost:5601/api/status
    depends_on:
      - kibana
```

Double-click the dgrkt portable executable or run `dgrkt up` in your current directory's terminal.

# Implementations

- [how-to-make-digirunner-opensource-launch-standalone](https://github.com/vulcanshen-tpi/how-to-make-digirunner-opensource-launch-standalone)
