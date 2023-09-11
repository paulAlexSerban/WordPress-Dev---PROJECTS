# Architecture Document for Log Processing and Google Sheets Integration Application

## System Overview

The system will be comprised of multiple independent microservices, each responsible for a particular aspect of the application. We will be using Node.js for building all our microservices.

### Components:

1. **S3 Log Fetcher Service**
2. **Log Sorting Service**
3. **CSV to JSON Parser Service**
4. **Monthly JSON Merger Service**
5. **Google Sheets Service**

## Architecture Diagram

```
                          +--------------+
                          | S3 Bucket    |
                          +------+-------+
                                 |
                                 |
                  +--------------v--------------+
                  | S3 Log Fetcher Service      |
                  +--------------+--------------+
                                 |
                                 |
                  +--------------v--------------+
                  | Log Sorting Service         |
                  +--------------+--------------+
                                 |
                                 |
                  +--------------v--------------+
                  | CSV to JSON Parser Service  |
                  +--------------+--------------+
                                 |
                                 |
                  +--------------v--------------+
                  | Monthly JSON Merger Service |
                  +--------------+--------------+
                                 |
                                 |
                  +--------------v--------------+
                  | Google Sheets Service       |
                  +--------------+--------------+
                                 |
                                 |
                          +------v-------+
                          | Google Sheet |
                          +--------------+
```

## Microservices Description

### S3 Log Fetcher Service

-   **Purpose**: Fetches logs from an AWS S3 bucket.
-   **Technology**: Node.js, AWS SDK
-   **Input**: Bucket name, log directory
-   **Output**: Raw logs
-   **API**: RESTful API to initiate log fetching

### Log Sorting Service

-   **Purpose**: Sorts logs by date.
-   **Technology**: Node.js
-   **Input**: Raw logs
-   **Output**: Sorted logs
-   **API**: RESTful API to initiate log sorting

### CSV to JSON Parser Service

-   **Purpose**: Parses logs from CSV to JSON.
-   **Technology**: Node.js, csv-parser
-   **Input**: Sorted logs
-   **Output**: Parsed logs in JSON
-   **API**: RESTful API to initiate log parsing

### Monthly JSON Merger Service

-   **Purpose**: Merges each month's logs into a single JSON file.
-   **Technology**: Node.js
-   **Input**: Parsed logs in JSON
-   **Output**: Merged monthly logs in JSON
-   **API**: RESTful API to initiate monthly log merging

### Google Sheets Service

-   **Purpose**: Pushes logs to Google Sheet.
-   **Technology**: Node.js, Google Sheets API
-   **Input**: Merged monthly logs in JSON
-   **Output**: Data pushed to Google Sheet
-   **API**: RESTful API to initiate data pushing

## Communication

-   Microservices will communicate with each other over HTTP/HTTPS using REST APIs.
-   Each microservice will emit events upon completion which will be captured by the next microservice in the pipeline.

## Error Handling

-   Each microservice will implement its own error handling and logging mechanism.
-   In case of an error, the microservice will return an appropriate HTTP status code along with an error message.

## Security

-   All internal communications will be encrypted using HTTPS.
-   AWS credentials will be securely stored and rotated.
-   Google Sheets API will be secured using OAuth 2.0.

## Deployment

-   Each microservice will be containerized using Docker.
-   The containerized applications will be orchestrated using Kubernetes.

## Monitoring and Logging

-   Prometheus will be used for monitoring the microservices.
-   ELK stack (Elasticsearch, Logstash, Kibana) will be used for logging and visualization.

## Future Improvements

1. Implement caching for frequently accessed logs.
2. Add a rate-limiting mechanism to APIs.
3. Include data validation and transformation steps.

This architecture provides a scalable and modular approach to building a log processing pipeline that ends with pushing the data to Google Sheets. Each component is loosely coupled and responsible for its own set of operations, making it easier to maintain and update.
