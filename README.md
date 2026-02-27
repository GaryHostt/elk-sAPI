# ELK MCP Project v2

This Mule application provides HTTP flows based on the OpenAPI specification for the Local ELK Stack API, implementing endpoints for Elasticsearch search operations and ML anomaly detection.

## Project Structure

### Main HTTP Flows
- **elk-search-logs-http-flow**: Implements `POST /{index}/_search` endpoint
- **elk-anomaly-results-http-flow**: Implements `POST /_ml/anomaly_detectors/{job_id}/_results` endpoint

### Tool Proxy Flows
- **tool-search-logs-flow**: Proxy endpoint at `POST /tools/searchLogs`
- **tool-anomaly-results-flow**: Proxy endpoint at `POST /tools/getAnomalyResults`

### Reusable Sub-flows
- **elk-search-logs-logic-flow**: Shared business logic for search operations
- **elk-anomaly-results-logic-flow**: Shared business logic for anomaly detection

## API Endpoints

### Search Logs
- **URL**: `POST http://localhost:8081/{index}/_search`
- **Tool URL**: `POST http://localhost:8081/tools/searchLogs`
- **Description**: Search documents in Elasticsearch index
- **Default Index**: `my-index`

### Anomaly Detection Results
- **URL**: `POST http://localhost:8081/_ml/anomaly_detectors/{job_id}/_results`
- **Tool URL**: `POST http://localhost:8081/tools/getAnomalyResults`
- **Description**: Get ML anomaly detection results

## Configuration

Configuration is managed through `src/main/resources/config.properties`:

```properties
# HTTP Configuration
http.host=0.0.0.0
http.port=8081

# Elasticsearch Configuration
elasticsearch.host=localhost
elasticsearch.port=9200
elasticsearch.url=http://localhost:9200

# Default Index Configuration
elasticsearch.default.index=my-index
```

## Running the Application

1. **Build the project**:
   ```bash
   mvn clean compile
   ```

2. **Run locally**:
   ```bash
   mvn mule:run
   ```

3. **Test endpoints**:
   - Search endpoint: `curl -X POST http://localhost:8081/my-index/_search -H "Content-Type: application/json" -d '{"query":{"match_all":{}}}'`
   - Tool endpoint: `curl -X POST http://localhost:8081/tools/searchLogs -H "Content-Type: application/json" -d '{"index":"my-index","query":{"match_all":{}}}'`

## API Testing with Postman

A comprehensive Postman collection is included for testing all API endpoints:

### Postman Files
- **`ELK-MCP-Project-v2.postman_collection.json`**: Complete collection with all endpoints
- **`ELK-MCP-Project-v2.postman_environment.json`**: Environment variables for local development
- **`POSTMAN_COLLECTION_USAGE.md`**: Detailed usage guide and documentation

### Quick Setup
1. Import both JSON files into Postman
2. Select "ELK MCP Project v2 - Local Development" environment
3. Start the Mule application
4. Run the "Health Check Examples" to verify connectivity

### Collection Features
- **4 main endpoints** with sample requests
- **OpenAPI and Tool formats** for each operation
- **Built-in test validation** and error handling
- **Environment variables** for easy configuration
- **Comprehensive documentation** for each request

See `POSTMAN_COLLECTION_USAGE.md` for complete testing instructions and examples.

## Prerequisites

- Java 8 or later
- Maven 3.6+
- Elasticsearch running on localhost:9200
- Mule Runtime 4.7.0+

## Error Handling

The application includes comprehensive error handling for:
- 4xx client errors (bad requests, not found, unauthorized)
- 5xx server errors (Elasticsearch connectivity issues)

## Features

- **Dynamic Path Parameters**: Support for dynamic index names and job IDs
- **Modular Design**: Logic separated into reusable sub-flows
- **Comprehensive Logging**: Request/response logging throughout flows
- **Error Handling**: Proper HTTP status codes and error messages
- **Property-driven Configuration**: All endpoints and settings configurable
