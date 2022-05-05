# Kafka Producer Benchmark

Simple benchmark to compare different clients performance against similar configuration.

The project is relatively low tech and just dumping similar metrics against different client into files.

# Which metrics are captured ?

The following metrics are collected (average across all topic partitions):
* Send rate = Number of messages sent per seconds
* Duration spent in the local queue = How many ms messages stayed in the queue before being sent ?
* Batch size = Average batch size
* Request Rate = number of Produce Request per seconds
* Request Latency = Average latency of Produce Request
* Records per Produce Request = Average number of records per Produce Request

At the end of the test all the messages are flushed then the benchmark will display the number of messages sent, the duration and number of produce request made.

# How to run it ?
## Running all scenarios
To run all scenarios use the following command :
```
./run-scenarios.sh
```
The script will execute all the scenario files named `scenario-<description>.env` at the root of the project against each Kafka Producer clients registered in the project.

Execution logs will be dumped in `./target/scenario-<description>.env/<client-name>.txt`.

## Running a single scenario

To run a scenario use the following command :
```
./run-scenario.sh <myscenario>.env
```

Execution logs will be dumped in `./target/scenario-<description>.env/<client-name>.txt`.

# How to contribute ?
Have any idea to make this benchmark better ? Found a bug ?
Do not hesitate to report us via github issues and/or create a pull request.

## Adding a new scenario ?


## Adding a new client ?
We strongly recommend you to run your test against localhost:9092 you can leverage the default `docker-compose.yml` to have a development environment.

Make everything configurable via environment variable.
Default variables are :
* KAFKA_BOOTSTRAP_SERVERS=localhost
* NB_TOPICS=1
* REPLICATION_FACTOR=1
* NUMBER_OF_PARTITIONS=6
* MESSAGE_SIZE=200
* NB_MESSAGES=1000000
* REPORTING_INTERVAL=1000

Convert KAFKA_XXX into lowercase by replacing "_" with dots.
This will help to play with batch.size/linger.ms/etc...

By default each client implementation will need to capture metrics at regular interval (defined via REPORTING_INTERVAL).
The should be logged using the following format to make things easier to compare: 
```java
        logger.info("Sent rate = {}/sec, duration spent in queue = {}ms, batch size = {}, request rate = {}/sec, request latency avg = {}ms, records per ProduceRequest = {}", avgSendRate, queueTimeAvg, batchSizeAvg, requestRate, requestLatencyAvg, recordsPerRequestAvg);

```

At the end of the run make sure all messages are delivered (ex. by calling producer.flush().

At the end of the run make sure you produce a log starting with "REPORT" keyword, this will be displayed at when executing scenarios.
Example:
```java
    logger.info("REPORT: Produced %s with %s ProduceRequest in %s", lastTotalMsgsMetric, lastRequestCount, str(timedelta(seconds=end_time - start_time)))
```