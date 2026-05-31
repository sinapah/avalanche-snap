<h1 align="center">
  Avalanche
</h1>

<p align="center">
  <b>This is the snap for <a href="https://github.com/prometheus-community/avalanche">Prometheus Avalanche</a></b>, a metrics series generator for load testing Prometheus-compatible systems.
</p>

<p align="center">
  It can expose metrics over a scrape endpoint or send them to a remote write endpoint, making it useful for testing Prometheus, OpenTelemetry Collector, Thanos, Cortex, M3DB, VictoriaMetrics, and similar systems.
</p>

## Install

```bash
sudo snap install avalanche
```

([Don’t have snapd installed?](https://snapcraft.io/docs/core/install))

## Overview

This repository contains the packaging for the **Avalanche** snap.

Avalanche is a testing binary capable of generating metrics that can be:

- scraped from a Prometheus/OpenMetrics endpoint
- written to a Prometheus remote write target

This snap packages Avalanche as a managed service for Linux systems using Snap.

## Configuration

The snap reads runtime arguments from a writable configuration file at:

```text
/var/snap/avalanche/current/daemon_arguments
```

On first run, the snap copies a default example configuration to that location automatically.

The file defines a single shell variable, `ARGS`, which is passed to the Avalanche binary when the daemon starts.

Example:

```sh
ARGS="--metric-count=500 --label-count=10 --series-count=10 --port=9001"
```

To edit the configuration:

```bash
sudo editor /var/snap/avalanche/current/daemon_arguments
```

After making changes, restart the service:

```bash
sudo snap restart avalanche.daemon
```

Example options include:

- `--metric-count=500` — number of unique metrics to generate
- `--label-count=10` — number of labels per metric
- `--series-count=10` — number of series per metric
- `--series-interval=60` — change series every N seconds
- `--series-change-rate=0` — rate of series changes
- `--port=9001` — port to serve metrics on
- `--remote-url=""` — remote write target URL
- `--remote-write-interval=10s` — interval between remote write requests

For the complete list of supported flags, see the upstream project:

- https://github.com/prometheus-community/avalanche

## Running and managing the service

The snap runs Avalanche as a daemon named `avalanche.daemon`.

Useful commands:

```bash
sudo snap services avalanche
sudo snap start avalanche
sudo snap stop avalanche
sudo snap restart avalanche
sudo snap logs avalanche
```

## Usage

Once the daemon is running, Avalanche serves generated metrics according to the arguments configured in `daemon_arguments`.

For example, if configured with:

```sh
ARGS="--port=9001"
```

you can scrape metrics from:

```text
http://localhost:9001/metrics
```

If configured with `--remote-url`, Avalanche will push generated metrics to the specified Prometheus remote write endpoint instead.

## Data and persistence

This snap stores its writable runtime configuration under:

```text
/var/snap/avalanche/current/
```

The main configuration file is:

```text
/var/snap/avalanche/current/daemon_arguments
```

The snap wrapper creates this file automatically on first run by copying the bundled example configuration.

This repository does not define any additional persistent application data directories beyond the snap-managed writable area used for runtime configuration.

## Development

This repository contains the snap packaging for Avalanche, including:

- `snapcraft.yaml` — snap definition and build configuration
- `daemon_arguments` — example daemon argument file
- `snap_config_wrapper` — wrapper that initializes and loads runtime arguments

To build the snap locally, use standard Snapcraft workflows.

## License

See [LICENSE](LICENSE).