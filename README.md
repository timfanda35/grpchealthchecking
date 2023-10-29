# GRPC Health Checking

A GRPC Health Checking Test Application

Based on https://github.com/kubernetes/kubernetes/blob/master/test/images/agnhost/grpc-health-checking/grpc-health-checking.go

## Usage

Compile

```bash
make compile
```

Execute

```bash
./target/grpc-health-checking
```

It shows logs:

```
I1029 15:02:02.769553   85361 log.go:245] Http server starting to listen on :8080
I1029 15:02:02.770155   85361 log.go:245] gRPC server starting to listen on :8000
```

You can use `-h` to see the help:

```
Usage:
  grpc-health-checking [flags]

Flags:
      --delay-unhealthy-sec int        Number of seconds to delay before start reporting NOT_SERVING, negative value indicates never. (default -1)
  -h, --help                           help for grpc-health-checking
      --http-port int                  Port number for the /make-serving, /make-not-serving and /healthcheck. (default 8080)
      --log-flush-frequency duration   Maximum number of seconds between log flushes (default 5s)
      --port int                       Port number. (default 8000)
      --service string                 Service name to register the health check for.
  -v, --v Level                        number for the log level verbosity
      --vmodule moduleSpec             comma-separated list of pattern=N settings for file-filtered logging (only works for the default text log format)
```
 
## Access the endpoints

Set the HOST environment variable for convenience

```bash
HOST=localhost
```
We can use the `curl` to access HTTP Endpoint.

We can use the [grpcurl](https://github.com/fullstorydev/grpcurl) to access GRPC Endpoint.

### HTTP Endpoint: `/healthcheck`

Access HTTP Health Check

```bash
curl $HOST:8080/healthcheck
```

Output should be like:

```
2m55.563755666s 
```

### HTTP Endpoint: `/make-serving`

Make GRPC Health Check serving

```bash
curl $HOST:8080/make-serving
```

### HTTP Endpoint: `/make-not-serving`

Make GRPC Health Check not serving

```bash
curl $HOST:8080/make-not-serving
```

### GRPC Endpoint: `Services List`

List GRPC Services with reflection.

```bash
grpcurl -plaintext $HOST:8000 list
```

Output should be:

```
grpc.health.v1.Health
grpc.reflection.v1.ServerReflection
grpc.reflection.v1alpha.ServerReflection
```

### GRPC Endpoint: `GRPC Health Check`

Check GRPC Service Healthy status.

```bash
grpcurl -plaintext $HOST:8000 grpc.health.v1.Health/Check
```

If serving:

```
{
  "status": "SERVING"
}
```

Otherwise:

```
{
  "status": "NOT_SERVING"
}
```
