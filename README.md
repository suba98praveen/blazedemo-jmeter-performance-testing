# BlazeDemo Website Performance Testing with Apache JMeter

## Overview

This project demonstrates end-to-end website performance testing of the **BlazeDemo flight booking application** using **Apache JMeter**.

The test simulates a complete flight-booking journey:

1. Open the BlazeDemo home page
2. Search for flights
3. Select a flight
4. Submit the purchase form
5. Validate the purchase confirmation

The project includes parameterization, dynamic correlation, assertions, think time, baseline testing, load testing, CLI execution, JTL result generation, and HTML dashboard reporting.

## Tools & Technologies

- Apache JMeter 5.6.3
- HTTP / HTTPS
- CSV Data Set Config
- CSS Selector Extractor
- Response Assertion
- Duration Assertion
- JMeter CLI / Non-GUI Mode
- JTL Results
- JMeter HTML Dashboard
- PowerShell

## Project Structure

```text
BlazeDemo Website Performance Testing/
│
├── test-plan/
│   └── BlazeDemo Website Performance Testing.jmx
│
├── test-data/
│   └── routes.csv
│
├── results/
│   ├── baseline-1-user.jtl
│   └── load-test-10-users.jtl
│
├── reports/
│   ├── baseline-1-user/
│   └── load-test-10-users/
│
├── screenshots/
│   ├── 01-jmeter-test-plan.png
│   ├── 02-baseline-dashboard.png
│   ├── 03-load-test-dashboard.png
│   ├── 04-response-time-percentiles.png
│   └── 05-response-times-over-time.png
│
└── README.md
```

## Test Scenario

The JMeter test plan contains four main HTTP requests:

| Step | Request | Method |
|---|---|---|
| 01 | Open BlazeDemo Home Page | GET |
| 02 | Search Flights | POST |
| 03 | Select Flight | POST |
| 04 | Purchase Flight | POST |

### Endpoints

```text
GET  /
POST /reserve.php
POST /purchase.php
POST /confirmation.php
```

The common host configuration is stored in **HTTP Request Defaults**:

```text
Protocol: https
Server: blazedemo.com
```

## Parameterization

Flight routes are read from:

```text
test-data/routes.csv
```

Example:

```csv
Boston,London
Paris,Berlin
San Diego,New York
Philadelphia,Rome
Portland,Cairo
```

JMeter variables:

```text
${fromPort}
${toPort}
```

## Dynamic Correlation

The flight search response is processed using **CSS Selector Extractors**.

Dynamic values extracted from the search response include:

```text
flightId
flightPrice
airline
```

They are passed to the next request using:

```text
${flightId}
${flightPrice}
${airline}
${fromPort}
${toPort}
```

## Assertions

The purchase request contains validations for:

- Purchase confirmation text
- HTTP response code `200`
- Response-time threshold

Example confirmation validation:

```text
Thank you for your purchase today!
```

## Think Time

A **Constant Timer** is used in the load test to introduce approximately:

```text
1000 ms
```

of think time between requests.

# Test Configurations

## Baseline Test

```text
Virtual Users: 1
Ramp-up: 1 second
Loop Count: 5
Expected HTTP Samples: 20
```

## Load Test

```text
Virtual Users: 10
Ramp-up: 20 seconds
Loop Count: 1
Think Time: 1000 ms
Expected HTTP Samples: 40
```

The load level was intentionally kept moderate because BlazeDemo is a public demonstration website.

# Performance Results

## Baseline — 1 User

| Metric | Result |
|---|---:|
| Samples | 20 |
| Average Response Time | 521 ms |
| Minimum Response Time | 375 ms |
| Maximum Response Time | 1,808 ms |
| Error Rate | 0.00% |
| Throughput | 1.8 req/s |

## Load Test — 10 Users

| Metric | Result |
|---|---:|
| Samples | 40 |
| Average Response Time | 611.98 ms |
| Median | 414 ms |
| P90 | 678.70 ms |
| P95 | 2,173.20 ms |
| P99 | 4,897 ms |
| Minimum Response Time | 341 ms |
| Maximum Response Time | 4,897 ms |
| Error Rate | 0.00% |
| Throughput | 1.77 transactions/sec |
| APDEX | 0.800 |

## Request-Level Results — 10 User Test

| Request | Average | P95 | Max | Error |
|---|---:|---:|---:|---:|
| Open BlazeDemo Home Page | 1204.00 ms | 4897.00 ms | 4897 ms | 0.00% |
| Search Flights | 396.80 ms | 470.00 ms | 470 ms | 0.00% |
| Select Flight | 424.50 ms | 561.00 ms | 561 ms | 0.00% |
| Purchase Flight | 422.60 ms | 529.00 ms | 529 ms | 0.00% |

# Key Findings

- All 40 HTTP samples completed successfully in the final 10-user CLI run.
- The final load-test error rate was **0.00%**.
- Average response time increased from **521 ms** in the baseline test to **611.98 ms** under the 10-user load.
- The largest tail-latency spike occurred on the **Open BlazeDemo Home Page** request.
- The overall P95 response time reached approximately **2.17 seconds**.
- Search, flight selection, and purchase requests remained comparatively stable in the final load-test run.
- The test demonstrates how concurrent users can increase tail latency even when the overall error rate remains zero.

# Running the Tests

## GUI Mode

Open the JMeter test plan:

```text
test-plan/BlazeDemo Website Performance Testing.jmx
```

Use GUI mode for:

- Test-plan development
- Debugging
- Checking request/response data
- Verifying extractors and assertions

## CLI / Non-GUI Mode

### 10-User Load Test

```powershell
jmeter -n -t "test-plan\BlazeDemo Website Performance Testing.jmx" `
-l "results\load-test-10-users.jtl" `
-e -o "reports\load-test-10-users"
```

### Baseline Test

```powershell
jmeter -n -t "test-plan\BlazeDemo Website Performance Testing.jmx" `
-l "results\baseline-1-user.jtl" `
-e -o "reports\baseline-1-user"
```

Before running a scenario, enable the required Thread Group and disable the other Thread Group.

## Open the HTML Dashboard

```powershell
Start-Process ".\reports\load-test-10-users\index.html"
```

or:

```powershell
Start-Process ".\reports\baseline-1-user\index.html"
```

# Skills Demonstrated

- Performance Testing
- Load Testing
- Apache JMeter
- HTTP GET / POST testing
- Parameterization using CSV
- Dynamic correlation
- CSS Selector Extractors
- Assertions
- Think time
- Response-time analysis
- P90 / P95 / P99 analysis
- Throughput analysis
- Error-rate analysis
- Non-GUI JMeter execution
- JTL result generation
- HTML performance reporting

## Notes

This project uses **BlazeDemo**, a public demo application. The test load was deliberately kept modest and was not intended as an aggressive stress or capacity test.

The payment form was tested only with dummy test values. No real payment-card information was used.

## Author

Performance testing project created as a hands-on Apache JMeter portfolio project.
