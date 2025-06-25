# weather-forcast-mcp

A Python MCP server that provides real-time weather information and forecasts using the Open-Meteo API. This project is designed for integration with Claude Desktop, Cursor, and other MCP-compatible clients.

## Features
- Get current weather for any location (latitude/longitude)
- Easily extensible for forecasts and geocoding
- No API key required (uses Open-Meteo)
- Ready for use with Claude Desktop and MCP Inspector

## Quick Start

### Requirements
- Python 3.11+
- [uv](https://github.com/astral-sh/uv) (for dependency management)
- Claude Desktop (optional, for LLM integration)

### Setup
```sh
# Clone this repository
git clone https://github.com/mdhabibi/weather-forcast-mcp.git
cd weather-forcast-mcp/mcp-server-weather

# Initialize and activate a virtual environment
uv venv
source .venv/bin/activate

# Install dependencies
uv pip install -r requirements.txt  # or use 'uv add "mcp[cli]" httpx' if requirements.txt is missing
```

### Running the MCP Server
```sh
# From the mcp-server-weather directory
mcp dev main.py
```

- Open [http://localhost:5173](http://localhost:5173) to use the MCP Inspector.
- Use Claude Desktop or any MCP client to connect and test tools.

## Usage
- Use the `get_current_weather` tool with latitude and longitude to get real-time weather data.
- Extend the server with more tools (e.g., forecasts, geocoding) as needed.

## Documentation
See [MCP_SERVER_SETUP_AND_USAGE.md](./MCP_SERVER_SETUP_AND_USAGE.md) for detailed setup, usage, and troubleshooting instructions.

## License
MIT
