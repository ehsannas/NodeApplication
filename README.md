# For tracing:

1. Change `projectId: 'myproject-d5314',` to your own project (use application default credentials).

2. Run `./otelcol-contrib --config=config.yaml` with the following config.yaml:

```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
exporters:
  googlecloud:
    project: myproject-d5314
  logging:
    loglevel: debug
processors:
  batch:
  resourcedetection:
    detectors: [gcp]
    timeout: 10s
service:
  telemetry:
    logs:
      level: debug
  pipelines:
    traces:
      receivers: [otlp]
      processors: [batch]
      exporters: [logging, googlecloud]
```

3. Run:
```
npm install
npm run build
env OTEL_TRACES_EXPORTER=otlp \
OTEL_LOG_LEVEL=all \
OTEL_NODE_ENABLED_INSTRUMENTATIONS="fs,http,grpc" \
OTEL_NODE_RESOURCE_DETECTORS="none" \
node --require @opentelemetry/auto-instrumentations-node/register build/index.js   
```
