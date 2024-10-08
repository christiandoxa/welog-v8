# Welog

`Welog` is a logging library designed for Go applications, integrating with ElasticSearch and utilizing `logrus` for structured logging. It supports log management for Go applications running on popular web frameworks like Fiber and Gin, providing detailed request and response logging.

## Installation

To install `welog`, add the library to your Go project by running:

```bash
go get github.com/christiandoxa/welog
```

## Configuration

`Welog` uses a configuration struct to set up ElasticSearch connection parameters. The `Config` struct allows you to define the ElasticSearch URL, username, password, and index name:

```go
type Config struct {
    ElasticIndex    string
    ElasticURL      string
    ElasticUsername string
    ElasticPassword string
}
```

### Setting Configuration with `SetConfig`

The `SetConfig` function is used to set ElasticSearch connection parameters via environment variables. Ensure you call this function at the start of your application:

### Example Usage

Call `SetConfig` with your configuration details before initializing the middleware:

```go
config := welog.Config{
    ElasticIndex:    "your-index",
    ElasticURL:      "http://localhost:9200",
    ElasticUsername: "your-username",
    ElasticPassword: "your-password",
}

welog.SetConfig(config)
```

By calling `SetConfig`, you ensure that the logging library is properly configured to connect to your ElasticSearch instance, allowing detailed request and response logging to function as expected.

## Usage

### Middleware Setup in Fiber

To use the `welog` middleware in a Fiber application, set up the middleware with the configuration as follows:

```go
fiberConfig := fiber.Config{}
app := fiber.New(fiberConfig)
app.Use(welog.NewFiber(fiberConfig))
```

### Middleware Setup in Gin

To use the `welog` middleware in a Gin application, set up the middleware with the configuration as follows:

```go
router := gin.Default()
router.Use(welog.NewGin())
```

### Logging Client Requests

#### Logging Client Requests in Fiber

When using a custom Fiber client, you can log client requests with `welog` using the following method:

```go
welog.LogFiberClient(
    c,
    requestURL,
    requestMethod,
    requestContentType,
    requestHeader,
    requestBody,
    responseHeader,
    responseBody,
    responseStatus,
    requestTime,
    responseLatency,
)
```

- `c`: The Fiber context.
- Other parameters: Include details of the request and response, such as URL, method, headers, body, status, and timing.

#### Logging Client Requests in Gin

For custom logging of client requests within Gin, use the `LogGinClient` method:

```go
welog.LogGinClient(
    c,
    requestURL,
    requestMethod,
    requestContentType,
    requestHeader,
    requestBody,
    responseHeader,
    responseBody,
    responseStatus,
    requestTime,
    responseLatency,
)
```

- `c`: The Gin context.
- Other parameters: Include details of the request and response, such as URL, method, headers, body, status, and timing.

### Logging Outside of Handlers

If you need to log errors or other information outside of a Fiber or Gin handler, you can directly use the `logger.Logger()` instance:

```go
logger.Logger().Fatal(err)
```

### Logging Inside Handlers in Fiber

When logging within a Fiber handler, use the logger instance stored in the Fiber context to ensure consistent and contextual logging:

```go
c.Locals("logger").(*logrus.Entry).Error(err)
```

### Logging Inside Handlers in Gin

When logging within a Gin handler, use the logger instance stored in the Gin context to ensure consistent and contextual logging:

```go
c.MustGet("logger").(*logrus.Entry).Error(err)
```

## Sample Output Logging

Below is a sample output log generated by `logFiber` and `LogFiberClient` functions:

```json
{
  "level": "info",
  "msg": "",
  "requestAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36",
  "requestBody": {
    "key1": "value1",
    "key2": "value2"
  },
  "requestBodyString": "{\"key1\": \"value1\", \"key2\": \"value2\"}",
  "requestContentType": "application/json",
  "requestHeader": {
    "Content-Type": "application/json",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"
  },
  "requestHostName": "localhost",
  "requestId": "123456",
  "requestIp": "192.168.1.1",
  "requestMethod": "POST",
  "requestProtocol": "HTTP/1.1",
  "requestTimestamp": "2024-09-25T12:34:56.789Z",
  "requestUrl": "http://localhost/api/v1/resource",
  "responseBody": {
    "responseKey1": "responseValue1"
  },
  "responseBodyString": "{\"responseKey1\": \"responseValue1\"}",
  "responseHeader": {
    "Content-Type": "application/json; charset=utf-8"
  },
  "responseLatency": "150ms",
  "responseStatus": 200,
  "responseTimestamp": "2024-09-25T12:34:56.939Z",
  "responseUser": "unknown",
  "target": [
    {
      "targetRequestBody": {
        "param1": "value1"
      },
      "targetRequestBodyString": "{\"param1\": \"value1\"}",
      "targetRequestContentType": "application/json",
      "targetRequestHeader": {
        "Authorization": "Bearer some_token"
      },
      "targetRequestMethod": "GET",
      "targetRequestTimestamp": "2024-09-25T12:34:56.789Z",
      "targetRequestURL": "https://example.com/api/data",
      "targetResponseBody": {
        "dataKey": "dataValue"
      },
      "targetResponseBodyString": "{\"dataKey\": \"dataValue\"}",
      "targetResponseHeader": {
        "Content-Length": "123"
      },
      "targetResponseLatency": "200ms",
      "targetResponseStatus": 200,
      "targetResponseTimestamp": "2024-09-25T12:34:56.989Z"
    }
  ]
}
```

This log provides detailed information about the request and response, including headers, body, latency, status, and additional metadata, which is useful for debugging and monitoring the application.

## Contributing

Contributions to `welog` are welcome! If you have suggestions, bug reports, or want to contribute code, please create a pull request or open an issue on GitHub.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
