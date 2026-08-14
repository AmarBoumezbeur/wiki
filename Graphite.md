# Introduction


# Graphite


# Installation

## Docker

1. Create docker image and install
```text
docker run -d \
  --name graphite \
  --restart=always \
  -p 80:80 \
  -p 2003-2004:2003-2004 \
  -p 2023-2024:2023-2024 \
  -p 8125:8125/udp \
  -p 8126:8126 \
  graphiteapp/graphite-statsd
```
2. Check it's UP
```text
docker logs graphite --tail 50
curl -s http://localhost/ | head -5
```
3. Servers:
```text
80       → graphite-web UI + HTTP render API
2003     → carbon plaintext protocol (what you'll push metrics to)
2004     → carbon pickle protocol (batch, more efficient for high-volume)
8125/udp → statsd, if you go that route instead of raw carbon
```