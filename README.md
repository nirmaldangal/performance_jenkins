# Apache JMeter — Complete Reference Guide

> A comprehensive guide covering Performance Testing fundamentals, JMeter architecture, configuration, scripting, analysis, and enterprise-level best practices.

---

## Table of Contents

1. [Performance Testing Fundamentals](#1-performance-testing-fundamentals)
2. [Types of Performance Testing](#2-types-of-performance-testing)
3. [Key Performance Metrics](#3-key-performance-metrics)
4. [JMeter Architecture & Installation](#4-jmeter-architecture--installation)
5. [Thread Groups & Settings](#5-thread-groups--settings)
6. [Samplers & Controllers](#6-samplers--controllers)
7. [Parameterization with CSV Data Set](#7-parameterization-with-csv-data-set)
8. [HTTP Request Defaults & Header Manager](#8-http-request-defaults--header-manager)
9. [Think Times, Timers & Listeners](#9-think-times-timers--listeners)
10. [Summary Report Columns Explained](#10-summary-report-columns-explained)
11. [Non-GUI Mode for Heavy Load Testing](#11-non-gui-mode-for-heavy-load-testing)
12. [Analyzing Summary Reports & Results](#12-analyzing-summary-reports--results)
13. [Assertions, Extractors & Correlation](#13-assertions-extractors--correlation)
14. [CI/CD Integration & Distributed Testing](#14-cicd-integration--distributed-testing)
15. [Best Practices & Industry Standards](#15-best-practices--industry-standards)

---

## 1. Performance Testing Fundamentals

### What is Performance Testing?

Performance testing is a non-functional testing discipline used to evaluate the speed, scalability, stability, and responsiveness of a system under a given workload. It is not about finding bugs in functionality — it is about understanding how a system behaves under stress, load, and time.

### Why Performance Testing Matters

- Identifies bottlenecks before production deployment
- Validates system capacity against SLA (Service Level Agreements)
- Ensures reliability under peak and sustained load
- Reduces risk of system failure during high-traffic events
- Provides data-driven insights for infrastructure scaling decisions

### The Performance Testing Process

```
1. Requirement Analysis   → Define SLAs, user load, and acceptance criteria
2. Test Planning          → Choose tools, design scenarios, set environments
3. Script Development     → Create test scripts (JMeter test plans)
4. Test Execution         → Run tests in staging/pre-prod environment
5. Results Analysis       → Collect and interpret metrics
6. Reporting              → Document findings and recommendations
7. Re-testing             → Validate fixes and compare baselines
```

### Key Terminology

| Term | Definition |
|------|------------|
| **Throughput** | Number of requests processed per unit of time (requests/sec) |
| **Latency** | Time from sending a request to receiving the first byte of response |
| **Response Time** | Total time from sending a request to receiving the complete response |
| **Concurrency** | Number of users simultaneously interacting with the system |
| **Think Time** | Simulated pause between user actions (mimics real user behavior) |
| **Ramp-Up** | Time period to gradually increase virtual users to target count |
| **Throughput** | Transactions per second (TPS) the system processes |
| **Percentile (P90/P95/P99)** | X% of requests completed within this time threshold |
| **Baseline** | Reference test result used for future performance comparisons |
| **SLA** | Service Level Agreement — agreed performance targets |

---

## 2. Types of Performance Testing

### Load Testing
**Definition:** Tests system behavior under expected normal and peak load conditions.

- Validates application performance against defined SLAs
- Typically uses concurrent users matching production traffic estimates
- Goal: Confirm the system meets acceptable response times at known load levels

```
Example: 500 concurrent users for 30 minutes during simulated business hours
```

### Stress Testing
**Definition:** Pushes the system beyond normal operating capacity to find the breaking point.

- Identifies the maximum capacity before failure
- Observes how the system fails (graceful degradation vs. hard crash)
- Validates recovery behavior after overload is removed

```
Example: Gradually increasing from 500 → 5000 users until system degrades or fails
```

### Spike Testing
**Definition:** Simulates a sudden and extreme increase in load over a very short period.

- Tests the system's reaction to sudden traffic bursts
- Common use case: Flash sales, viral social media events, breaking news
- Evaluates auto-scaling responsiveness in cloud environments

```
Example: Jump from 100 to 5000 users in 30 seconds, then drop back
```

### Soak Testing (Endurance Testing)
**Definition:** Runs the system at a sustained load level for an extended duration (hours or days).

- Detects memory leaks, resource exhaustion, and connection pool depletion
- Validates system stability over time
- Reveals degradation patterns not visible in short tests

```
Example: 300 concurrent users continuously for 8–24 hours
```

### Volume Testing
**Definition:** Tests the system's ability to handle large amounts of data.

- Evaluates database performance with massive datasets
- Tests file processing with large uploads/downloads
- Validates batch processing performance

### Scalability Testing
**Definition:** Determines the system's ability to scale up (vertical) or scale out (horizontal).

- Tests the impact of adding more CPU, RAM, or nodes
- Validates linear vs. diminishing returns on infrastructure investment
- Used for capacity planning decisions

### Smoke Testing (Sanity Check)
**Definition:** A lightweight test to verify the test environment and basic functionality before full load testing.

- Run with 1–5 users
- Confirms scripts work correctly before heavier tests
- Validates environment stability

---

## 3. Key Performance Metrics

### Response Time Metrics

| Metric | Description | Acceptable Range (Typical) |
|--------|-------------|---------------------------|
| **Average Response Time** | Mean time across all requests | < 2 seconds (web), < 500ms (API) |
| **Median (P50)** | 50% of requests completed within this time | < 1 second |
| **P90 (90th Percentile)** | 90% of requests are faster than this value | < 3 seconds |
| **P95 (95th Percentile)** | 95% of requests are faster than this value | < 5 seconds |
| **P99 (99th Percentile)** | 99% of requests are faster than this value | < 10 seconds |
| **Min Response Time** | Fastest single request observed | — |
| **Max Response Time** | Slowest single request observed | — |

> **Why percentiles matter more than averages:** A 200ms average can hide the fact that 10% of users experience 10-second responses. Percentiles reveal the tail latency that affects real users.

### Throughput Metrics

| Metric | Description |
|--------|-------------|
| **Requests/Second (RPS)** | Number of HTTP requests completed per second |
| **Transactions/Second (TPS)** | Business transactions (e.g., logins, purchases) per second |
| **KB/Second** | Data transfer rate (bandwidth consumption) |

### Error Metrics

| Metric | Description | Target |
|--------|-------------|--------|
| **Error Rate (%)** | Percentage of failed requests | < 1% under load, 0% at normal load |
| **Error Count** | Total number of failed requests | — |
| **Error Types** | HTTP 4xx (client), 5xx (server), timeouts | All should be investigated |

### Resource Utilization Metrics (Server-Side)

| Metric | Description | Warning Threshold |
|--------|-------------|-------------------|
| **CPU Utilization** | Processor usage percentage | > 80% sustained |
| **Memory Usage** | RAM consumption | > 85% sustained |
| **GC Activity** | Java Garbage Collection frequency/duration | > 10% GC overhead |
| **Thread Pool** | Active vs. available threads | Near-exhaustion |
| **DB Connection Pool** | Active vs. max DB connections | > 90% utilization |
| **Disk I/O** | Read/write operations and latency | High await times |
| **Network Bandwidth** | Inbound/outbound traffic | Near capacity |

---

## 4. JMeter Architecture & Installation

### What is Apache JMeter?

Apache JMeter is an open-source, Java-based performance testing tool developed by the Apache Software Foundation. It was originally designed for web application testing but now supports a wide range of protocols including HTTP/HTTPS, FTP, JDBC, LDAP, SOAP, REST, TCP, and more.

### JMeter Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     JMeter Test Plan                         │
│                                                             │
│  ┌─────────────┐   ┌──────────────┐   ┌─────────────────┐  │
│  │ Thread Group│   │  Samplers    │   │   Listeners     │  │
│  │ (Virtual    │──▶│  (HTTP, JDBC,│──▶│  (Results Tree, │  │
│  │  Users)     │   │   FTP, etc.) │   │   Summary, etc.)│  │
│  └─────────────┘   └──────────────┘   └─────────────────┘  │
│         │                │                                   │
│  ┌──────▼──────┐  ┌──────▼──────┐                          │
│  │  Controllers│  │Config       │                           │
│  │ (Logic,     │  │Elements     │                           │
│  │  Loop, etc.)│  │(CSV, Header,│                           │
│  └─────────────┘  │ Defaults)   │                           │
│                   └─────────────┘                           │
│                                                             │
│  ┌──────────────┐  ┌─────────────┐  ┌──────────────────┐  │
│  │  Pre-        │  │  Post-      │  │   Assertions     │  │
│  │  Processors  │  │  Processors │  │ (Response Code,  │  │
│  │  (User Params│  │ (Regex,     │  │  Duration, etc.) │  │
│  │   BeanShell) │  │  JSON Path) │  │                  │  │
│  └──────────────┘  └─────────────┘  └──────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### JMeter Component Hierarchy

```
Test Plan
  └── Thread Group
        ├── Config Elements         (CSV Data Set, HTTP Defaults, Cookies)
        ├── Pre-Processors          (User Parameters, BeanShell)
        ├── Samplers                (HTTP Request, JDBC, FTP)
        │     ├── Assertions        (Response, Duration, Size)
        │     └── Post-Processors   (Regex Extractor, JSON Extractor)
        ├── Controllers             (Loop, If, Transaction, Module)
        ├── Timers                  (Constant, Gaussian, Uniform)
        └── Listeners               (Summary Report, Aggregate, Tree)
```

### Key JMeter Components

| Component | Category | Purpose |
|-----------|----------|---------|
| **Thread Group** | Core | Defines virtual users, ramp-up, and loop count |
| **HTTP Request** | Sampler | Sends HTTP/HTTPS requests to the server |
| **JDBC Request** | Sampler | Executes SQL queries directly against databases |
| **CSV Data Set Config** | Config Element | Reads test data from CSV files for parameterization |
| **HTTP Header Manager** | Config Element | Adds/overrides HTTP headers for requests |
| **HTTP Request Defaults** | Config Element | Sets default values shared across all HTTP samplers |
| **Response Assertion** | Assertion | Validates response content, code, or size |
| **JSON Path Extractor** | Post-Processor | Extracts values from JSON responses |
| **Regex Extractor** | Post-Processor | Extracts values using regular expressions |
| **Constant Timer** | Timer | Adds fixed delay between requests |
| **Gaussian Timer** | Timer | Adds random delay using normal distribution |
| **Summary Report** | Listener | Shows aggregated test statistics |
| **View Results Tree** | Listener | Shows individual request/response details |

### Installation

**Prerequisites:**
- Java JDK 8 or higher (JDK 11+ recommended)
- Minimum 4 GB RAM (8+ GB recommended for heavy tests)
- Set `JAVA_HOME` environment variable

**Installation Steps:**

```bash
# 1. Verify Java installation
java -version

# 2. Download JMeter (replace X.X with latest version)
wget https://downloads.apache.org/jmeter/binaries/apache-jmeter-5.6.3.tgz

# 3. Extract
tar -xvzf apache-jmeter-5.6.3.tgz

# 4. Navigate to bin directory
cd apache-jmeter-5.6.3/bin

# 5. Launch GUI (Linux/Mac)
./jmeter.sh

# Launch GUI (Windows)
jmeter.bat
```

**JMeter Directory Structure:**

```
apache-jmeter-5.6.x/
├── bin/              ← Startup scripts, jmeter.properties
├── lib/              ← Core JAR files
│   └── ext/          ← Plugin JARs go here
├── docs/             ← Documentation
├── extras/           ← Ant integration, sample scripts
└── printable_docs/   ← Printable user manual
```

---

## 5. Thread Groups & Settings

### What is a Thread Group?

A Thread Group is the entry point for every JMeter test plan. It defines the virtual user pool — the number of simulated users, how fast they start, and how long/how many times they run.

### Thread Group Properties

| Property | Definition |
|----------|------------|
| **Number of Threads (Users)** | Total virtual users to simulate concurrently |
| **Ramp-Up Period (seconds)** | Time to gradually start all threads. JMeter starts `Threads / Ramp-Up` new threads each second |
| **Loop Count** | How many times each thread executes the test plan. Use `Infinite` for duration-based testing |
| **Duration (seconds)** | Total test run time when using the Scheduler (overrides loop count) |
| **Startup Delay (seconds)** | Pause before threads begin (useful in distributed testing) |
| **On Sample Error** | Action when an error occurs: Continue / Start Next Loop / Stop Thread / Stop Test |

### Thread Group Calculation Example

```
Goal: Simulate 100 users over 5 minutes (300 seconds)

Settings:
  Number of Threads : 100
  Ramp-Up Period    : 60  (1 new user every 0.6 seconds)
  Duration          : 300 (run for 5 minutes total)
  Loop Count        : [checked] Infinite

JMeter starts: 100 users / 60 seconds = ~1.67 new threads/second
After 60s: All 100 users are active and running for remaining 240s
```

### Types of Thread Groups

| Type | Use Case |
|------|----------|
| **Thread Group** | Standard — most common for all load scenarios |
| **setUp Thread Group** | Runs before all other thread groups (login, test data prep) |
| **tearDown Thread Group** | Runs after all thread groups (logout, cleanup) |
| **Concurrency Thread Group** *(plugin)* | Maintains exact concurrency level by replacing finished threads |
| **Stepping Thread Group** *(plugin)* | Steps load up/down in defined increments |
| **Ultimate Thread Group** *(plugin)* | Fully custom load shape with multiple steps |

### Scheduler Configuration

Enable the **Scheduler** checkbox in the Thread Group to use time-based execution:

```
Start Time    : (optional) schedule future test start
End Time      : (optional) schedule test end
Duration      : run for N seconds (overrides loop count)
Startup Delay : wait N seconds before starting threads
```

---

## 6. Samplers & Controllers

### Samplers

Samplers are the elements that actually send requests to the server. Each sampler generates one sample result.

#### HTTP Request Sampler (Most Common)

```
Protocol    : http / https
Server Name : api.example.com
Port Number : 443
Method      : GET / POST / PUT / DELETE / PATCH
Path        : /api/v1/users
```

**Body Data (POST/PUT):**
```json
{
  "username": "${username}",
  "password": "${password}"
}
```

**Parameters Tab:** Add query string or form parameters as key-value pairs.

#### Other Common Samplers

| Sampler | Protocol | Use Case |
|---------|----------|----------|
| **HTTP Request** | HTTP/HTTPS | Web and REST API testing |
| **JDBC Request** | SQL | Database query performance testing |
| **FTP Request** | FTP | File transfer testing |
| **SMTP Sampler** | SMTP | Email system testing |
| **TCP Sampler** | TCP/IP | Raw socket-level testing |
| **JSR223 Sampler** | Groovy/Java | Custom scripting logic |
| **Debug Sampler** | — | Displays variable values during debug runs |

### Controllers

Controllers determine the order and logic of sampler execution.

#### Logic Controllers

| Controller | Function |
|------------|----------|
| **Simple Controller** | Groups samplers for organization (no logic change) |
| **Loop Controller** | Repeats child samplers N times or infinitely |
| **If Controller** | Executes children only if a condition is true |
| **While Controller** | Loops while a condition remains true |
| **Switch Controller** | Routes to one of N controllers based on a value |
| **Random Controller** | Randomly selects one child sampler per iteration |
| **Random Order Controller** | Executes all children in random order |
| **Transaction Controller** | Groups samplers into a single timed transaction |
| **Module Controller** | Reuses test fragments from elsewhere in the plan |
| **Include Controller** | Imports external `.jmx` test plan files |
| **ForEach Controller** | Loops over a set of variables (e.g., extracted list items) |
| **Interleave Controller** | Alternates child samplers across loop iterations |

#### Transaction Controller — Important Usage

```
✔ Generate Parent Sample   → Creates a combined result for all child samplers
✔ Include Duration of Timer → Include timer delays in transaction time

Use case: Group Login → Dashboard → Search into a single "User Journey" transaction
```

---

## 7. Parameterization with CSV Data Set

### What is Parameterization?

Parameterization replaces hardcoded values in test scripts with dynamic data. This enables:
- Testing with multiple user credentials
- Simulating diverse search queries
- Using unique transaction IDs per request
- Avoiding cached responses by varying inputs

### CSV Data Set Config

**Location:** Right-click Thread Group → Add → Config Element → CSV Data Set Config

#### CSV Data Set Config Settings

| Field | Description |
|-------|-------------|
| **Filename** | Absolute or relative path to the CSV file |
| **File Encoding** | UTF-8 recommended |
| **Variable Names** | Comma-separated names matching CSV columns (e.g., `username,password,email`) |
| **Ignore first line** | Set to `True` if CSV has a header row |
| **Delimiter** | Separator character (default: `,`) |
| **Allow quoted data** | Enable if values contain commas inside quotes |
| **Recycle on EOF** | `True` = loop back to start when all rows are used |
| **Stop thread on EOF** | `True` = stop thread when data runs out |
| **Sharing Mode** | Controls how threads share data (see below) |

#### Sharing Mode Options

| Mode | Behavior |
|------|----------|
| **All threads** | Single shared pointer — each row used by one thread globally |
| **Current thread group** | Separate pointer per thread group |
| **Current thread** | Each thread reads its own copy independently |

#### Sample CSV File (`test_users.csv`)

```csv
username,password,expected_role
admin@test.com,Admin@123,ADMIN
user1@test.com,User@123,USER
manager@test.com,Mgr@456,MANAGER
guest@test.com,Guest@789,GUEST
```

#### Using CSV Variables in Samplers

In an HTTP Request body:
```json
{
  "email": "${username}",
  "password": "${password}"
}
```

In an Assertion to validate role:
```
Response Body Contains: ${expected_role}
```

### Other Parameterization Methods

| Method | Use Case |
|--------|----------|
| **CSV Data Set Config** | Large datasets from files |
| **User Defined Variables** | Static values shared across thread groups |
| **User Parameters (Pre-Processor)** | Per-thread variable assignments |
| **__Random()** function | Random integer in a range: `${__Random(1,100,)}` |
| **__RandomString()** | Random string: `${__RandomString(8,abcdefghij,)}` |
| **__UUID()** | Unique ID per request: `${__UUID()}` |
| **__time()** | Current timestamp: `${__time(yyyy-MM-dd HH:mm:ss,)}` |
| **__counter()** | Incrementing counter: `${__counter(FALSE,)}` |

---

## 8. HTTP Request Defaults & Header Manager

### HTTP Request Defaults

**Location:** Right-click Thread Group → Add → Config Element → HTTP Request Defaults

HTTP Request Defaults set common values shared across all HTTP Samplers within scope. Individual samplers can override these values.

#### Commonly Configured Fields

| Field | Example Value |
|-------|---------------|
| **Protocol** | `https` |
| **Server Name or IP** | `api.example.com` |
| **Port Number** | `443` |
| **Content Encoding** | `UTF-8` |
| **Path (optional)** | `/api/v1` (base path prefix) |
| **Connect Timeout** | `5000` (ms) |
| **Response Timeout** | `30000` (ms) |

> **Best Practice:** Always set timeouts in HTTP Request Defaults. Without timeouts, failed requests wait indefinitely, causing thread starvation and misleading results.

### HTTP Header Manager

**Location:** Right-click Thread Group (or Sampler) → Add → Config Element → HTTP Header Manager

The Header Manager injects HTTP headers into requests. It can be placed at the Test Plan, Thread Group, or individual Sampler level.

#### Common Headers

```
Content-Type        : application/json
Accept              : application/json
Authorization       : Bearer ${access_token}
X-Correlation-ID    : ${__UUID()}
X-Api-Key           : ${api_key}
Accept-Language     : en-US
Cache-Control       : no-cache
```

#### Scope Behavior

```
Test Plan Level       → Headers applied to ALL samplers in the plan
Thread Group Level    → Headers applied to all samplers in that group
Individual Sampler    → Headers applied only to that specific request
```

### HTTP Cookie Manager

Automatically manages cookies across requests (session persistence):

```
Clear cookies each iteration : ✔ (recommended — ensures clean session per user)
Cookie Policy                : standard
```

### HTTP Cache Manager

Simulates browser caching behavior:

```
Clear cache each iteration : ✔ (disable to test cache hit performance)
Max Number of Elements     : 5000
```

---

## 9. Think Times, Timers & Listeners

### Think Time (Timers)

Think time simulates the realistic pause a user makes between actions (reading a page, filling a form, making a decision). Without think time, JMeter sends requests as fast as possible, creating an unrealistic load pattern.

**Where to add timers:** Right-click on Thread Group, Controller, or Sampler → Add → Timer

### Timer Types

#### Constant Timer
Adds a fixed, unchanging delay before every sampler in scope.

```
Thread Delay : 3000 ms  →  Always waits exactly 3 seconds
```

#### Uniform Random Timer
Adds a random delay within a defined range.

```
Constant Delay Offset    : 1000 ms
Random Delay Maximum     : 2000 ms
→ Actual delay = random(0–2000) + 1000 ms  →  Range: 1–3 seconds
```

#### Gaussian Random Timer
Adds delay based on a normal (bell-curve) distribution — most realistic for simulating human behavior.

```
Deviation    : 500 ms    (standard deviation)
Constant     : 2000 ms   (mean/center of distribution)
→ Most delays cluster around 2000ms, with natural variation
```

#### Constant Throughput Timer
Controls request rate to maintain a target TPS (transactions per second).

```
Target Throughput (in samples/min) : 600   →  10 requests/second
Calculate Throughput Based On      : All active threads in current thread group
```

#### Synchronizing Timer
Holds threads until N threads are waiting, then releases them simultaneously — useful for burst testing.

```
Number of Simulated Users to Group : 50
Timeout in milliseconds             : 10000
→ Wait until 50 threads arrive, then release all at once
```

### Listeners

Listeners collect and display test results. They consume memory and CPU — **disable or remove them for heavy load tests; use Non-GUI mode with file output instead.**

| Listener | Use Case |
|----------|----------|
| **View Results Tree** | Debug: shows full request/response for each sample |
| **Summary Report** | Aggregated statistics per sampler (label) |
| **Aggregate Report** | Similar to Summary but adds median and percentile columns |
| **Response Time Graph** | Visual graph of response time over test duration |
| **Active Threads Over Time** | Graph of concurrent user count |
| **Transactions Per Second** | TPS graph over time (requires plugin) |
| **jp@gc - Response Times Percentiles** | Percentile distribution (requires plugin) |
| **Simple Data Writer** | Write raw results to a JTL/CSV file (lightweight) |
| **Backend Listener** | Stream results in real-time to InfluxDB/Graphite |

> **Rule:** For any test with > 100 users, disable View Results Tree. Use Simple Data Writer with CSV output for post-analysis.

---

## 10. Summary Report Columns Explained

The **Summary Report** is the most commonly used listener for quick analysis. Each row represents a unique sampler label (or "TOTAL" for all combined).

### Column Reference

| Column | Full Name | Definition |
|--------|-----------|------------|
| **Label** | Sampler Label | Name of the HTTP Request or Transaction Controller |
| **# Samples** | Sample Count | Total number of requests sent during the test |
| **Average** | Average Response Time | Mean response time in milliseconds across all samples |
| **Min** | Minimum Response Time | Fastest individual request time (ms) |
| **Max** | Maximum Response Time | Slowest individual request time (ms) |
| **Std. Dev.** | Standard Deviation | Measure of response time consistency; high value = high variability |
| **Error %** | Error Percentage | Percentage of requests that resulted in an error (4xx, 5xx, timeouts, assertion failures) |
| **Throughput** | Requests/Second | Number of requests processed per second (overall test average) |
| **Received KB/sec** | Data Received | Average kilobytes received from server per second |
| **Sent KB/sec** | Data Sent | Average kilobytes sent to server per second |
| **Avg. Bytes** | Average Response Size | Mean response body size in bytes |

### Interpreting the Summary Report

```
Label               #Samples  Average  Min  Max   Std.Dev  Error%  Throughput
---------------------------------------------------------------------------
Login               500       245      80   1200  180      0.20%   16.5/sec
Search Products     500       380      100  2100  290      0.40%   15.8/sec
Add to Cart         500       290      90   900   150      0.00%   16.2/sec
Checkout            500       520      150  3500  410      1.20%   15.1/sec
TOTAL               2000      358      80   3500  280      0.45%   64.1/sec

Analysis:
✅ Login and Add to Cart: Fast and stable (low Std.Dev, zero/near-zero errors)
⚠️  Search Products: High Max (2100ms), worth investigating P95/P99
❌  Checkout: 1.20% errors exceed 1% SLA — investigate immediately
❌  Checkout Max (3500ms): Potential timeout or DB bottleneck
```

### Standard Deviation Interpretation

| Std. Dev. vs. Average | Meaning |
|-----------------------|---------|
| Std. Dev. < 50% of Average | Consistent response times — healthy |
| Std. Dev. 50–100% of Average | Moderate variability — monitor closely |
| Std. Dev. > Average | High variability — instability detected |

---

## 11. Non-GUI Mode for Heavy Load Testing

### Why Non-GUI Mode?

The JMeter GUI consumes significant memory and CPU (often 20–30% of test machine resources). For realistic load tests with 100+ users, always run in Non-GUI (command-line) mode:

- Reduces JMeter overhead, giving more resources to virtual users
- Enables automation and CI/CD integration
- Supports scheduled and remote execution
- Required for distributed testing

### Basic Non-GUI Commands

#### Run a Test Plan

```bash
jmeter -n -t TestPlan.jmx -l results.jtl -e -o ./reports/
```

| Flag | Meaning |
|------|---------|
| `-n` | Non-GUI mode |
| `-t <file>` | Path to the JMX test plan file |
| `-l <file>` | Path to the results log file (JTL/CSV format) |
| `-e` | Generate HTML report after test |
| `-o <dir>` | Output directory for HTML report (must be empty or new) |

#### Override JMeter Properties at Runtime

```bash
jmeter -n \
  -t TestPlan.jmx \
  -l results_$(date +%Y%m%d_%H%M%S).jtl \
  -Jusers=200 \
  -Jrampup=60 \
  -Jduration=300 \
  -Jhost=staging.api.example.com \
  -e -o ./html_report/
```

In the Test Plan, reference these properties:
```
Number of Threads : ${__P(users,10)}        ← default 10 if not passed
Ramp-Up Period    : ${__P(rampup,30)}
Duration          : ${__P(duration,60)}
```

#### Specify a Log File

```bash
jmeter -n -t TestPlan.jmx -l results.jtl -j jmeter_run.log
```

| Flag | Meaning |
|------|---------|
| `-j <file>` | JMeter execution log (different from results file) |

#### Run with Remote Engines (Distributed)

```bash
jmeter -n -t TestPlan.jmx -R server1,server2,server3 -l results.jtl
```

#### Generate HTML Report from Existing JTL File

```bash
jmeter -g results.jtl -o ./html_report/
```

### Full Production-Grade Command

```bash
#!/bin/bash
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
RESULTS_DIR="./test_results/${TIMESTAMP}"
mkdir -p "${RESULTS_DIR}"

jmeter -n \
  -t ./scripts/FullSuiteTest.jmx \
  -l "${RESULTS_DIR}/results.jtl" \
  -j "${RESULTS_DIR}/jmeter.log" \
  -Jthreads=500 \
  -Jrampup=120 \
  -Jduration=600 \
  -Jenv=staging \
  -Jbase_url=https://staging.api.example.com \
  -e -o "${RESULTS_DIR}/html_report" \
  | tee "${RESULTS_DIR}/console.log"

echo "Test complete. Results: ${RESULTS_DIR}"
```

### jmeter.properties Tuning (for heavy tests)

Edit `apache-jmeter/bin/jmeter.properties`:

```properties
# Increase summary log interval (default 30s)
summariser.interval=10

# Reduce result file size by limiting stored data
jmeter.save.saveservice.output_format=csv
jmeter.save.saveservice.response_data=false
jmeter.save.saveservice.samplerData=false
jmeter.save.saveservice.requestHeaders=false
jmeter.save.saveservice.responseHeaders=false
jmeter.save.saveservice.successful=false

# Keep only failures for large tests
jmeter.save.saveservice.successful=false
jmeter.save.saveservice.print_field_names=true
```

---

## 12. Analyzing Summary Reports & Results

### HTML Dashboard Report

JMeter's built-in HTML report (`-e -o ./report`) generates:

- **Statistics Table:** All metrics per sampler (avg, median, P90, P95, P99, max, error%)
- **Response Time Over Time:** Line graph showing trends across test duration
- **Latency Over Time:** Time from request send to first byte
- **Connect Time Over Time:** TCP connection establishment time
- **Transactions Per Second:** Throughput graph
- **Response Time Percentiles:** Distribution chart
- **Active Users Over Time:** Concurrency visualization
- **Top 5 Errors:** Most frequent failure messages

### Reading the Aggregate Report

The Aggregate Report (vs. Summary Report) adds three critical columns:

```
Median  : P50 — 50% of users experienced this response time or better
90% Line: P90 — The SLA benchmark column; most orgs target "90% < 3s"
95% Line: P95 — Industry standard for premium SLAs
99% Line: P99 — Tail latency; identifies the worst-case experienced users
```

### Performance Baseline Comparison

```
Metric             | Baseline | Current Run | Delta  | Status
-------------------|----------|-------------|--------|--------
Avg Response Time  | 220ms    | 245ms       | +11%   | ⚠️ Watch
P90 Response Time  | 480ms    | 510ms       | +6%    | ✅ Pass
Error Rate         | 0.10%    | 0.45%       | +350%  | ❌ Fail
Throughput (RPS)   | 18.2     | 16.5        | -9%    | ⚠️ Watch
```

### Common Performance Problems and Indicators

| Symptom | Likely Cause |
|---------|-------------|
| Response time increases linearly with users | Insufficient server resources (CPU/RAM) |
| Error rate spikes after a threshold | Thread pool, DB connection pool, or queue exhaustion |
| High Std. Dev. on a specific endpoint | Intermittent external dependency (3rd party API, slow query) |
| Throughput plateaus before target | Application bottleneck reached |
| Memory grows continuously (Soak test) | Memory leak in application |
| Slow only first request per user | Session initialization overhead |
| High P99 but low average | Outlier requests hitting slow code paths |

---

## 13. Assertions, Extractors & Correlation

### Assertions

Assertions validate that server responses meet expected criteria. Failed assertions increment the Error % in reports.

#### Response Assertion

```
Apply to     : Main sample only
Field to test: Response Code / Response Message / Response Body / Response Headers
Pattern Type : Contains / Matches / Equals / Substring / Not
Pattern      : 200  (for Response Code assertion)
               "userId"  (for body content check)
```

#### Duration Assertion

```
Duration in milliseconds : 3000
→ Fails any request taking longer than 3 seconds
→ Use to enforce SLA at the sampler level
```

#### JSON Assertion (JSON Path)

```
JSONPath expression    : $.data.status
Additionally assert value : true
Expected value         : active
```

#### Size Assertion

```
Field to test  : Response Body
Type of comparison : >
Size in bytes  : 0
→ Fails on empty response bodies (useful for detecting silent failures)
```

### Post-Processors (Extractors)

Extractors capture values from responses and store them in JMeter variables for use in subsequent requests — this is called **correlation**.

#### JSON Path Extractor

```
Name of created variable    : access_token
JSONPath expression         : $.data.token
Match No.                   : 1   (first match; -1 = random; 0 = all)
Default Value               : TOKEN_NOT_FOUND
```

Subsequent use in HTTP Request:
```
Authorization Header: Bearer ${access_token}
```

#### Regular Expression Extractor

```
Reference Name         : session_id
Regular Expression     : "sessionId":"([^"]+)"
Template               : $1$
Match No.              : 1
Default Value          : SESSION_NOT_FOUND
```

#### Boundary Extractor

```
Reference Name  : csrf_token
Left Boundary   : name="_token" value="
Right Boundary  : "
Match No.       : 1
```

#### CSS/jQuery Extractor

```
Reference Name  : page_title
CSS/JQuery      : h1.page-title
Attribute       : (empty = inner text)
```

### Correlation Flow (Full Example)

```
Step 1: POST /api/auth/login
  Body: {"email": "${username}", "password": "${password}"}
  → JSON Extractor: $.data.access_token  →  saves to: access_token
  → JSON Extractor: $.data.user.id       →  saves to: user_id

Step 2: GET /api/users/${user_id}/profile
  Header: Authorization: Bearer ${access_token}
  → Asserts: Response Code = 200
  → JSON Extractor: $.profile.role  →  saves to: user_role

Step 3: POST /api/orders
  Header: Authorization: Bearer ${access_token}
  Body: {"userId": "${user_id}", "items": [...]}
  → Duration Assertion: < 3000ms
```

---

## 14. CI/CD Integration & Distributed Testing

### CI/CD Integration

#### Jenkins Integration

**Basic Jenkinsfile:**
```groovy
pipeline {
    agent any
    stages {
        stage('Performance Test') {
            steps {
                sh """
                    jmeter -n \
                      -t tests/load/api_load_test.jmx \
                      -l results/results_${BUILD_NUMBER}.jtl \
                      -Jenv=${params.ENV} \
                      -Jthreads=${params.THREADS} \
                      -Jduration=${params.DURATION} \
                      -e -o results/html_${BUILD_NUMBER}
                """
            }
        }
        stage('Validate Results') {
            steps {
                // Fail build if error rate > 1%
                sh """
                    python3 validate_results.py \
                      results/results_${BUILD_NUMBER}.jtl \
                      --max-error-rate 1.0 \
                      --max-avg-response 2000
                """
            }
        }
    }
    post {
        always {
            publishHTML([
                reportDir: "results/html_${BUILD_NUMBER}",
                reportFiles: 'index.html',
                reportName: 'JMeter Performance Report'
            ])
        }
    }
}
```

#### GitHub Actions Integration

```yaml
name: Performance Tests

on:
  push:
    branches: [main]
  workflow_dispatch:
    inputs:
      threads:
        default: '50'
      duration:
        default: '120'

jobs:
  performance:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Java
        uses: actions/setup-java@v3
        with:
          java-version: '11'
          distribution: 'temurin'
      
      - name: Download JMeter
        run: |
          wget -q https://downloads.apache.org/jmeter/binaries/apache-jmeter-5.6.3.tgz
          tar -xzf apache-jmeter-5.6.3.tgz
          echo "$PWD/apache-jmeter-5.6.3/bin" >> $GITHUB_PATH
      
      - name: Run Performance Tests
        run: |
          jmeter -n \
            -t tests/api_test.jmx \
            -l results.jtl \
            -Jthreads=${{ github.event.inputs.threads || 50 }} \
            -Jduration=${{ github.event.inputs.duration || 120 }} \
            -e -o ./perf_report
      
      - name: Upload Results
        uses: actions/upload-artifact@v3
        with:
          name: jmeter-results
          path: |
            results.jtl
            perf_report/
```

### Distributed (Remote) Testing

Distributed testing uses a **Controller** (master) and one or more **Engines** (slaves) to generate load across multiple machines.

#### Architecture

```
                     ┌─────────────────┐
                     │  Controller PC  │
                     │  (JMeter GUI /  │
                     │   Command Line) │
                     └────────┬────────┘
                              │ RMI
               ┌──────────────┼──────────────┐
               ▼              ▼              ▼
        ┌────────────┐ ┌────────────┐ ┌────────────┐
        │  Engine 1  │ │  Engine 2  │ │  Engine 3  │
        │ (jmeter-   │ │ (jmeter-   │ │ (jmeter-   │
        │  server)   │ │  server)   │ │  server)   │
        └────────────┘ └────────────┘ └────────────┘
          100 users      100 users      100 users
                      = 300 total users
```

#### Setup Steps

**On each Engine/Slave machine:**
```bash
# Edit jmeter.properties
server.rmi.ssl.disable=true          # For internal networks
server_port=1099

# Start server mode
jmeter-server -Djava.rmi.server.hostname=<ENGINE_IP>
```

**On the Controller:**
```bash
# Edit jmeter.properties
remote_hosts=192.168.1.101,192.168.1.102,192.168.1.103

# Run distributed test
jmeter -n -t TestPlan.jmx -r -l results.jtl
# OR specify engines explicitly:
jmeter -n -t TestPlan.jmx -R 192.168.1.101,192.168.1.102 -l results.jtl
```

**Thread Count in Distributed Mode:**
```
Test Plan setting: 100 threads
3 engines:         100 × 3 = 300 total concurrent users
```

### JMeter Plugins (Recommended)

Install via **Plugin Manager** (`JMeter Plugins Manager.jar` → drop in `/lib/ext/`):

| Plugin | Purpose |
|--------|---------|
| **jpgc - Standard Set** | Includes TPS, Response Time, Active Threads graphs |
| **jpgc - Concurrency Thread Group** | Maintains exact concurrency level |
| **jpgc - Stepping Thread Group** | Stepped load increase/decrease |
| **jpgc - Custom Thread Groups** | Ultimate Thread Group for complex load shapes |
| **jpgc - PerfMon** | Server-side resource monitoring (CPU, RAM, Disk, Net) |
| **jpgc - Merging & Splitting** | Merge/split JTL result files |

---

## 15. Best Practices & Industry Standards

### Test Design Best Practices

**1. Use Non-GUI mode for actual load testing**
```bash
# Development/debugging: GUI is fine
# Any test with > 50 users: use Non-GUI mode
jmeter -n -t TestPlan.jmx -l results.jtl
```

**2. Parameterize everything — no hardcoded values**
```
❌ Bad:  Server = "https://prod.example.com" (hardcoded)
✅ Good: Server = "${__P(base_url,https://staging.example.com)}"
```

**3. Always set connection and response timeouts**
```
Connect Timeout  : 5000 ms
Response Timeout : 30000 ms
```

**4. Include think time in all user journey tests**
```
Minimum: Constant Timer of 1–3 seconds between page requests
Realistic: Gaussian Random Timer (mean=3000ms, deviation=1000ms)
```

**5. Clean up sessions between iterations**
```
HTTP Cookie Manager → Clear cookies each iteration: ✔
HTTP Cache Manager  → Clear cache each iteration:   ✔
```

**6. Never run tests against production** unless specifically planned, approved, and during off-peak hours with rollback capability.

**7. Use Transaction Controllers for business flows**
```
Wrap related samplers in Transaction Controllers:
  "User Login Flow"     → Login + Redirect + Dashboard Load
  "Product Search Flow" → Search + Filter + Sort + View Product
```

**8. Always run a Smoke Test first**
```
1 thread, 1 loop → Confirm scripts work and environment is healthy
Then scale to full load
```

### Result Analysis Best Practices

**1. Focus on percentiles, not just averages**
```
P90 < 3 seconds  → Standard web application SLA
P95 < 5 seconds  → Premium SLA target
Error Rate < 1%  → Maximum acceptable error rate under load
```

**2. Compare against baselines**
- Store results from every test run
- Flag any metric that degrades > 10% from baseline

**3. Correlate with server-side metrics**
- JMeter shows symptoms; server metrics reveal causes
- Use PerfMon plugin or APM tools (Datadog, New Relic, Dynatrace) alongside JMeter

**4. Never draw conclusions from a single test run**
- Run 3 identical tests; average the results
- Discard anomalous outliers with documented justification

### Infrastructure Best Practices

**JMeter Machine Sizing:**

| Users | RAM | CPU | Notes |
|-------|-----|-----|-------|
| < 100 | 4 GB | 2 cores | Single machine, GUI acceptable |
| 100–500 | 8 GB | 4 cores | Non-GUI required |
| 500–2000 | 16 GB | 8 cores | Non-GUI, tune JVM heap |
| 2000+ | Distributed | Multiple machines | 500–1000 users per engine |

**JVM Heap Tuning (`jmeter` startup script):**
```bash
# Default: -Xms1g -Xmx1g
# For 500+ users, increase:
JVM_ARGS="-Xms4g -Xmx4g -XX:MaxMetaspaceSize=512m"
```

**Disable unnecessary Listeners during load tests:**
```
❌ View Results Tree     → High memory, disable during load tests
❌ Aggregate Graph       → Use Simple Data Writer instead
✅ Simple Data Writer    → Minimal overhead, writes raw JTL
✅ Backend Listener      → Stream to InfluxDB/Grafana for live monitoring
```

### Industry Standards & SLA Guidelines

#### General Web Application Targets

| Metric | Green | Yellow | Red |
|--------|-------|--------|-----|
| Average Response Time | < 1s | 1–3s | > 3s |
| P90 Response Time | < 2s | 2–5s | > 5s |
| P95 Response Time | < 3s | 3–8s | > 8s |
| Error Rate | < 0.5% | 0.5–1% | > 1% |
| CPU Utilization | < 60% | 60–80% | > 80% |
| Memory Usage | < 70% | 70–85% | > 85% |

#### API Service Targets (REST/Microservices)

| Endpoint Type | P50 Target | P99 Target |
|---------------|------------|------------|
| Simple read (GET) | < 100ms | < 500ms |
| Complex query | < 500ms | < 2000ms |
| Write/update (POST/PUT) | < 200ms | < 1000ms |
| File upload/download | < 2000ms | < 10000ms |

### Test Script Maintenance Checklist

```
□  All hardcoded values replaced with variables/properties
□  CSV files parameterize unique user data
□  Think times applied to all user journeys
□  Connection and response timeouts configured
□  Assertions on every critical sampler
□  Correlation implemented for all dynamic tokens/IDs
□  Transaction Controllers group business flows
□  setUp/tearDown thread groups handle data prep/cleanup
□  Non-GUI mode used for all load tests
□  Results saved to versioned output files
□  HTML report generated after each test
□  Baseline results stored for comparison
□  Test plan under version control (Git)
```

---

## Quick Reference Card

### JMeter Non-GUI Cheat Sheet

```bash
# Basic run
jmeter -n -t plan.jmx -l results.jtl

# With HTML report
jmeter -n -t plan.jmx -l results.jtl -e -o ./report

# Override properties
jmeter -n -t plan.jmx -l results.jtl -Jusers=100 -Jduration=300

# Distributed run
jmeter -n -t plan.jmx -R host1,host2 -l results.jtl

# Generate report from existing results
jmeter -g results.jtl -o ./report

# Run with custom log file
jmeter -n -t plan.jmx -l results.jtl -j run.log
```

### Key JMeter Functions

```
${__P(property,default)}     ← Read command-line property
${__V(variable_name)}        ← Get variable value dynamically
${__time(yyyy-MM-dd,)}       ← Current date/time
${__UUID()}                  ← Generate unique UUID
${__Random(1,100,)}          ← Random integer between 1–100
${__counter(FALSE,)}         ← Auto-incrementing counter
${__base64Encode(text,)}     ← Base64 encode a string
${__md5(text,)}              ← MD5 hash of a string
${__eval(${my_variable})}    ← Force variable evaluation
${__groovy(expression,)}     ← Execute Groovy inline
```

### Performance Testing Decision Matrix

```
Scenario                     | Test Type
-----------------------------|------------------------
Expected peak traffic        | Load Test
Find maximum capacity        | Stress Test
Flash sale / viral event     | Spike Test
Memory leaks over time       | Soak/Endurance Test
Database with 10M records    | Volume Test
Adding more servers          | Scalability Test
Validate scripts work        | Smoke Test
```

---

*Apache JMeter is a trademark of the Apache Software Foundation. This guide covers JMeter 5.x.*
