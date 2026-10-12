# CLASSIFIED

### Market Data Infrastructure · Real-Time Streaming · Multi-Timeframe Intelligence

**A unified market-data infrastructure package for exchange connectivity, normalized OHLCV data, multi-timeframe requests, and HTTP/WebSocket delivery.**

CLASSIFIED provides shared market-data infrastructure for charting applications, analytical engines, and specialist trading systems. It centralizes exchange connectivity and market-data handling so in[...]

The package is published under the distribution name `classifiedai`, while its Python import namespace remains `classified`.

> **Core principle:** One market-data foundation. Multiple independent consumers. A consistent data contract.

---

## Features

- **Exchange connectivity** — integrate with supported exchanges through CCXT.
- **Normalized OHLCV data** — work with Open, High, Low, Close, and Volume candle data.
- **Multi-timeframe requests** — request market data at different candle intervals.
- **Historical market data** — retrieve candles for charting and analysis.
- **Adaptive candle requirements** — centralize candle-count requirements for data consumers where supported by the active implementation.
- **HTTP API** — expose market data through HTTP endpoints.
- **WebSocket streaming** — support persistent connections for live market-data delivery.
- **Shared infrastructure** — allow independent applications to reuse the same market-data foundation.

CLASSIFIED is infrastructure, not a trading strategy or trade-execution engine.

---

## Installation

Install the package from PyPI:

```bash
python -m pip install classifiedai
```

### Requirements

- Python 3.12 or newer.
- Network access to the configured exchange where live exchange data is required.
- Appropriate exchange configuration for the selected data source.

### Verify the installation

```bash
python -c "import classified; print(classified.__file__)"
```

The distribution name used by `pip` is `classifiedai`. The import namespace used in Python remains `classified`.

### Install optional test dependencies

```bash
python -m pip install "classifiedai[test]"
```

This command is available when the published release includes the `test` optional dependency extra.

---

## Architecture

```text
                  EXCHANGE DATA SOURCE
                          |
                          v
                +----------------------+
                |   CLASSIFIED Core    |
                |                      |
                | Exchange Connectivity|
                | OHLCV Normalization  |
                | Multi-Timeframe Data |
                | Candle Requirements  |
                | Cache & Freshness    |
                +----------+-----------+
                           |
                           v
                +----------------------+
                |     API Gateway      |
                |                      |
                | HTTP / REST          |
                | WebSocket Streaming  |
                +----------+-----------+
                           |
              +------------+------------+
              |            |            |
              v            v            v
         Visualization  Analytical   Other Data
          Applications   Systems     Consumers
```

*Conceptual architecture. The precise responsibilities and available capabilities depend on the installed implementation and its configuration.*

---

## Market Data

CLASSIFIED is designed to provide a common interface for market-data consumers that need historical candles, multiple timeframes, or continuous updates.

### OHLCV data

OHLCV represents:

| Field | Meaning |
|---|---|
| Open | Opening price for a candle |
| High | Highest price reached during the candle |
| Low | Lowest price reached during the candle |
| Close | Closing price for a candle |
| Volume | Volume recorded during the candle |

### Multi-timeframe access

Example candle intervals include:

| Timeframe | Typical analytical use |
|---|---|
| `4h` | Higher-timeframe market structure |
| `1h` | Intermediate market context |
| `15m` | Intraday price action |
| `5m` | Short-term price monitoring |

These are example intervals, not trading signals. Actual supported intervals and candle limits depend on the exchange and implementation.

---

## API Gateway

The gateway provides HTTP-based access and WebSocket streaming through an asynchronous server using `aiohttp`.

The documented local server address is:

```text
http://127.0.0.1:8080
```

The server must be started separately when using the gateway endpoints.

### Start the gateway

From an environment where CLASSIFIED and its dependencies are installed, run:

```bash
python -m classified.api.server
```

The server is configured to listen on port `8080` in the documented setup.

### 1. Health check

**Method:** `GET`

```http
GET /health
```

Local URL:

```text
http://127.0.0.1:8080/health
```

Use this endpoint to check whether the gateway is responding.

### 2. Historical candle data

**Method:** `GET`

```http
GET /api/v1/market-data/candles
```

Query parameters:

| Parameter | Description | Example |
|---|---|---|
| `symbol` | Trading pair | `BTC/USDT` |
| `timeframe` | Candle interval | `5m` |
| `candle_count` | Requested candle count | `150` |

Example URL:

```text
http://127.0.0.1:8080/api/v1/market-data/candles?symbol=BTC%2FUSDT&timeframe=5m&candle_count=150
```

PowerShell example:

```powershell
Invoke-RestMethod `
  "http://127.0.0.1:8080/api/v1/market-data/candles?symbol=BTC%2FUSDT&timeframe=5m&candle_count=150"
```

The endpoint is intended to supply historical candle data to charting and analytical consumers. Consult the active server implementation for the exact response schema and error behavior.

### 3. Live market-data stream

**Protocol:** WebSocket

```text
ws://127.0.0.1:8080/api/v1/market-data/stream
```

Query parameters:

| Parameter | Description | Example |
|---|---|---|
| `symbol` | Trading pair | `BTC/USDT` |
