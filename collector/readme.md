## 1. Install the OpenTelemetry Collector Builder

Download and set up the OpenTelemetry Collector Builder (`ocb`):

```bash
curl --proto '=https' --tlsv1.2 -fL -o ocb \
    https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/cmd%2Fbuilder%2Fv0.122.1/ocb_0.122.1_linux_amd64
chmod +x ocb
```

## 2. Build a Custom Collector

Use the installed builder to generate a custom OpenTelemetry Collector based on your configuration file:

```bash
./ocb --config ocb-cfg.yaml
```

## 3. Build and Push Docker Image

Build the Docker image for your custom OpenTelemetry Collector:

```bash
docker build -t <repo>/otelcol-dev:0.1.0 .
```

Push the image to your repository:

```bash
docker push <repo>/otelcol-dev:0.1.0
```
