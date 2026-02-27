# ELK MCP Project v2 - Postman Collection Usage Guide

This guide provides instructions for using the Postman collection to test and interact with the ELK MCP Project v2 API.

## Quick Start

### 1. Import the Collection and Environment

1. **Import the Collection**: 
   - Open Postman
   - Click "Import" → "Upload Files"
   - Select `ELK-MCP-Project-v2.postman_collection.json`

2. **Import the Environment**:
   - Click "Import" → "Upload Files"
   - Select `ELK-MCP-Project-v2.postman_environment.json`
   - Select "ELK MCP Project v2 - Local Development" from the environment dropdown

### 2. Start the Mule Application

Before testing, ensure your Mule application is running:

```bash
cd /path/to/elk-mcp-project-v2
mvn mule:run
```

The application will start on `http://localhost:8081` by default.

### 3. Verify Elasticsearch is Running

Ensure Elasticsearch is running on `localhost:9200`:

```bash
curl -X GET "localhost:9200/_cluster/health?pretty"
```

## Collection Structure

The collection is organized into three main folders:

### 📁 Search Operations
- **Search Logs (OpenAPI)**: Standard Elasticsearch API endpoint
- **Search Logs (Tool)**: Simplified tool interface

### 📁 Anomaly Detection  
- **Get Anomaly Results (OpenAPI)**: Standard ML API endpoint
- **Get Anomaly Results (Tool)**: Simplified tool interface

### 📁 Health Check Examples
- **Basic Search Test**: Simple connectivity test
- **Tool Search Test**: Tool endpoint connectivity test

## API Endpoints Overview

| Endpoint | Method | Purpose | Format |
|----------|--------|---------|---------|
| `/{index}/_search` | POST | OpenAPI search | Elasticsearch DSL |
| `/tools/searchLogs` | POST | Tool search | Simplified JSON |
| `/_ml/anomaly_detectors/{job_id}/_results` | POST | OpenAPI anomaly | Elasticsearch ML |
| `/tools/getAnomalyResults` | POST | Tool anomaly | Simplified JSON |

## Environment Variables

The collection uses these environment variables:

| Variable | Default Value | Description |
|----------|---------------|-------------|
| `baseUrl` | `http://localhost:8081` | API base URL |
| `index` | `my-index` | Default Elasticsearch index |
| `job_id` | `my-anomaly-job` | Default ML job ID |
| `test_index` | `test-logs` | Test index name |
| `test_job_id` | `test-anomaly-job` | Test job ID |

### Customizing Variables

1. Go to Environment settings in Postman
2. Modify values as needed for your setup
3. Variables are automatically applied to requests

## Sample Requests

### 1. Basic Search (OpenAPI Format)

**Endpoint**: `POST /{{index}}/_search`

**Request Body**:
```json
{
  "query": {
    "match_all": {}
  },
  "size": 10,
  "from": 0,
  "sort": [
    {
      "@timestamp": {
        "order": "desc"
      }
    }
  ]
}
```

### 2. Search with Filters (OpenAPI Format)

**Request Body**:
```json
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "@timestamp": {
              "gte": "2024-01-01T00:00:00Z",
              "lte": "2024-12-31T23:59:59Z"
            }
          }
        },
        {
          "match": {
            "level": "ERROR"
          }
        }
      ]
    }
  },
  "size": 50
}
```

### 3. Tool Search (Simplified Format)

**Endpoint**: `POST /tools/searchLogs`

**Request Body**:
```json
{
  "index": "application-logs",
  "query": {
    "query": {
      "match": {
        "service": "user-service"
      }
    },
    "size": 20,
    "sort": [
      {
        "@timestamp": {
          "order": "desc"
        }
      }
    ]
  }
}
```

### 4. Anomaly Detection (OpenAPI Format)

**Endpoint**: `POST /_ml/anomaly_detectors/{{job_id}}/_results`

**Request Body**:
```json
{
  "sort": "record_score",
  "desc": true,
  "start": "2024-01-01T00:00:00Z",
  "end": "2024-12-31T23:59:59Z",
  "anomaly_score_filter": {
    "gte": 75
  }
}
```

### 5. Anomaly Detection (Tool Format)

**Endpoint**: `POST /tools/getAnomalyResults`

**Request Body**:
```json
{
  "job_id": "cpu-usage-anomaly-job",
  "sort": "timestamp",
  "desc": true,
  "start": "2024-01-01T00:00:00Z",
  "end": "2024-12-31T23:59:59Z",
  "anomaly_score_filter": {
    "gte": 50
  }
}
```

## Testing Workflow

### Basic Connectivity Test

1. **Start with Health Check Examples**:
   - Run "Basic Search Test" to verify the OpenAPI endpoint
   - Run "Tool Search Test" to verify the tool endpoint

2. **Check Response Status**:
   - 200: Success
   - 400: Bad request (check request format)
   - 500: Server error (check Elasticsearch connectivity)

### Progressive Testing

1. **Test Simple Queries First**:
   - Use `match_all` queries with small result sets
   - Verify basic connectivity and response format

2. **Test Complex Queries**:
   - Add filters, sorting, and aggregations
   - Test different indices and parameters

3. **Test Error Scenarios**:
   - Invalid index names
   - Malformed queries
   - Non-existent job IDs

## Troubleshooting

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Connection refused | Mule app not running | Start with `mvn mule:run` |
| 500 errors | Elasticsearch down | Start Elasticsearch service |
| 404 errors | Wrong endpoint path | Check URL pattern |
| 400 errors | Invalid JSON | Validate request body syntax |

### Debug Tips

1. **Check Postman Console**: View request/response details
2. **Monitor Mule Logs**: Check application logs for errors  
3. **Test Elasticsearch Directly**: Verify Elasticsearch is responding
4. **Validate JSON**: Ensure request bodies are valid JSON

### Response Examples

**Successful Search Response**:
```json
{
  "took": 5,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 100,
      "relation": "eq"
    },
    "max_score": 1.0,
    "hits": [...]
  }
}
```

**Error Response Example**:
```json
{
  "error": {
    "type": "client_error",
    "reason": "Bad request to Elasticsearch"
  }
}
```

## Advanced Usage

### Custom Environments

Create additional environments for different setups:

1. **Development**: `http://localhost:8081`
2. **Staging**: `http://staging-api:8081`
3. **Production**: `https://prod-api.company.com`

### Automated Testing

The collection includes built-in tests that run automatically:

- Response status validation
- Content-Type header verification
- Error response logging

### Collection Variables

You can also use collection variables for request-specific data:

```javascript
// In Pre-request Script
pm.collectionVariables.set("currentTimestamp", new Date().toISOString());
```

## Integration with CI/CD

### Newman Command Line

Run the collection from command line using Newman:

```bash
# Install Newman
npm install -g newman

# Run collection
newman run ELK-MCP-Project-v2.postman_collection.json \
  -e ELK-MCP-Project-v2.postman_environment.json \
  --reporters cli,json \
  --reporter-json-export results.json
```

### Docker Testing

```bash
# Run with Docker
docker run -t postman/newman:latest \
  run ELK-MCP-Project-v2.postman_collection.json \
  -e ELK-MCP-Project-v2.postman_environment.json
```

## Security Considerations

- **Local Development**: Default configuration is for local testing
- **Production**: Update `baseUrl` and add authentication headers if needed
- **Elasticsearch Access**: Ensure proper network security for Elasticsearch

## Support

For issues with:
- **Mule Application**: Check application logs and configuration
- **Elasticsearch**: Verify cluster health and index existence  
- **Postman Collection**: Review request format and variables
- **API Responses**: Check Mule flow logging and error handling

## Collection Maintenance

### Updating Requests

1. Modify requests in Postman
2. Export updated collection
3. Replace the JSON file
4. Update this documentation as needed

### Version Control

- Track collection and environment files in version control
- Document changes in commit messages
- Maintain separate environments for different stages
