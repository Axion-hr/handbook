---
description: Below is a guide for basic endpoint stress testing.
---

# REST API stress testing



## JMeter installation

To install jmeter on your mac type following in the terminal:

```
brew install jmeter
```

To install JMeter on Ubuntu server type in the following as `root` user:
```
apt get install jmeter
```

## JMeter usage

Jmeter can be used through GUI or through CLI. GUI usage is more suited to create test scenarios while CLI usage is more suited for running on servers that have connectivity to endpoint that is being tested.

### Running JMeter in console

```
jmeter -n -t stress_test.jmx -l stress_test.result.csv -p local.properties
```

where:
 * stress_test.jmx is file generated in JMeter GUI
 * stress_test.result.csv is file where results of the test are written
 * local.properties is file that contains variables that can be used in stress test, e.g. which server to connect to, how many times to repeat the test etc.

### Creating a test case

To start JMeter with GUI, just type `jmeter` in console. Please open stress test in `lp-be-cupo-service` to see how the test is structred. Main components of the test are added by right clicing on the tree on the left, or component of the tree on the left. To execute a successful request, the following needs to be defined:
 * headers (e.g. for authentiacation)
 * method and path
 * host 
 * optional query parameters / request body


TBD
