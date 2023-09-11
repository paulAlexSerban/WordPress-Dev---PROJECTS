# Sheetify Logs

## Introduction

-   scalable and robust microservices-based application that fetches logs from an AWS S3 bucket, sorts them by date, parses the logs from CSV to JSON format, merges each month's logs into a single JSON file, and finally pushes the data into a Google Sheet.
-   API is designed to automate the retrieval, parsing, and storage of log files from AWS S3 to Google Sheets. The application architecture employs a microservices-based approach. Each microservice is responsible for a specific functionality: Retrieving logs, parsing logs, merging logs, and pushing data to Google Sheets.
-   designed to fetch logs from AWS S3, sort them by date, parse the CSV logs to JSON format, merge logs for each month into a single JSON file, and finally push the data into a Google Sheet. This architecture employs a microservices approach and is developed using Node.js.
