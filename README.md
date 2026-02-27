# ELK MCP Project v2

This Mule application provides HTTP flows based on the OpenAPI specification for the Local ELK Stack API, implementing endpoints for Elasticsearch search operations and ML anomaly detection.

## Project Structure

### Elk HTTP Flows (OpenAPI)
- **elk-search-logs-http-flow**: Implements `POST /{index}/_search` — used by Postman and by the MCP tool flow
- **elk-anomaly-results-http-flow**: Implements `POST /_ml/anomaly_detectors/{job_id}/_results` — used by Postman and by the MCP tool flow

### MCP Tool Flows (agent integration)
- **tool-search-logs-flow**: MCP connector tool-listener for `searchLogs`; transforms tool args and flow-refs `elk-search-logs-http-flow`
- **tool-anomaly-results-flow**: MCP connector tool-listener for `getAnomalyResults`; transforms tool args and flow-refs `elk-anomaly-results-http-flow`

## API Endpoints

### Search Logs
- **OpenAPI URL**: `POST http://localhost:8081/{index}/_search`
- **MCP Tool**: `searchLogs` (for agent integration)
- **Description**: Search documents in Elasticsearch index
- **Default Index**: `my-index`

### Anomaly Detection Results
- **OpenAPI URL**: `POST http://localhost:8081/_ml/anomaly_detectors/{job_id}/_results`
- **MCP Tool**: `getAnomalyResults` (for agent integration)
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
   - Anomaly endpoint: `curl -X POST http://localhost:8081/_ml/anomaly_detectors/my-job/_results -H "Content-Type: application/json" -d '{"sort":"record_score","desc":true}'`

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
- **Elk HTTP (OpenAPI) endpoints** only: search and anomaly results
- **Built-in test validation** and error handling
- **Environment variables** for easy configuration
- **Comprehensive documentation** for each request
- Agent access to the same backend is via MCP tools (`searchLogs`, `getAnomalyResults`)

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
