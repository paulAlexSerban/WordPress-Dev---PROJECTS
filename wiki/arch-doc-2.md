# Architecture Document & System Design for Log Management API

---

## Architecture

### Microservices

1. **Log Retrieval Service**
2. **CSV to JSON Parser Service**
3. **Log Merger Service**
4. **Google Sheets Service**

### Other Components

1. **API Gateway**
2. **Message Queue**
3. **Scheduler**

### Tech Stack

- Node.js for backend services
- AWS SDK for interaction with S3
- Google Sheets API for updating Google Sheets
- RabbitMQ for Message Queue
- API Gateway for service orchestration

---

### Architecture Diagram

```
                       +---------------+
                       |   Scheduler   |
                       +-------+-------+
                               |
                               | Trigger
                               v
                       +-------+-------+
                       | API Gateway   |-------------------------+
                       +-------+-------+                         |
                               |                                |
            +------------------+--------------------+           |
            |                  |                    |           |
            v                  v                    v           v
  +---------+--------+   +-----+-----+      +--------+-----+  +--+------+
  | Log Retrieval    |   | CSV to   |      | Log Merger   |  | Google  |
  | Service          |-->| JSON     |----->| Service      |->| Sheets  |
  |                  |   | Parser   |      |              |  | Service |
  +------------------+   +----------+      +--------------+  +---------+
```

---

## Detailed Design

### 1. Log Retrieval Service

- **Responsibility**: Retrieve logs from AWS S3.
  
#### Endpoints:
  - `/fetchLogs`: Triggered by API Gateway to fetch logs from S3.

#### Implementation:
  - Use AWS SDK to interact with S3.

### 2. CSV to JSON Parser Service

- **Responsibility**: Parse CSV logs into JSON format.

#### Endpoints:
  - `/parseCSV`: Accepts CSV data and returns JSON.

#### Implementation:
  - Use csv-parse npm package for parsing.

### 3. Log Merger Service

- **Responsibility**: Merge logs sorted by date into monthly JSON files.

#### Endpoints:
  - `/mergeLogs`: Accepts multiple JSON logs and returns a single merged JSON.

#### Implementation:
  - JSON logs are sorted by date and merged into a single JSON.

### 4. Google Sheets Service

- **Responsibility**: Push JSON data into Google Sheets.

#### Endpoints:
  - `/pushToSheet`: Accepts JSON and updates Google Sheets.

#### Implementation:
  - Use Google Sheets API for updating sheets.

### 5. API Gateway

- **Responsibility**: Orchestrating the services.

#### Implementation:
  - Triggered by the Scheduler or a user request.
  - Makes RESTful calls to the individual services.

### 6. Message Queue (RabbitMQ)

- **Responsibility**: Decouples services.

#### Implementation:
  - Queue messages from one service to another.
  
### 7. Scheduler

- **Responsibility**: Scheduling log retrieval.

#### Implementation:
  - Uses cron jobs to schedule API Gateway triggers.

---

## Data Flow

1. Scheduler triggers the API Gateway at regular intervals.
2. API Gateway invokes the Log Retrieval Service to fetch logs from S3.
3. Log Retrieval Service puts logs into a message queue.
4. CSV to JSON Parser Service picks up logs and parses them to JSON, then puts them back into a message queue.
5. Log Merger Service picks up JSON logs, sorts and merges them into monthly JSONs, and puts them into a message queue.
6. Google Sheets Service picks up the merged JSON logs and pushes them into Google Sheets.

---

## Scalability & Future Enhancements

1. **Distributed Message Queue**: To handle high throughput.
2. **Load Balancing**: Use multiple instances of microservices with a load balancer.

---

## Deployment

Docker containers managed by Kubernetes for each microservice.

---

This document is meant to serve as a high-level guide and may require additional specifications for implementation.