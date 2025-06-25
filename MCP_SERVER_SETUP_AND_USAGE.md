# MCP Weather Server Setup & Usage Guide

This guide will help you set up, run, and test an MCP server that provides real-time weather information using the Open-Meteo API. The server is designed for integration with Claude Desktop and Cursor, but can be adapted for other MCP clients.

---

## 0. Requirements

- **Claude.ai account** (MCP support available for all account types)
- **Claude Desktop app** (macOS/Windows)
- **A code editor** (e.g., Visual Studio Code, Cursor)
- **uv** (Rust-based Python package manager)
  - macOS: `brew install uv`
  - Windows: `winget install --id=astral-sh.uv  -e`

---

## 1. Project Setup

1. **Create project folder:**
    ```sh
    mkdir mcp-server-weather
    cd mcp-server-weather
    ```
2. **Initialize a new uv project:**
    ```sh
    uv init
    ```
3. **Create and activate a virtual environment:**
    ```sh
    uv venv
    source .venv/bin/activate
    # (On Windows: .venv\Scripts\activate)
    ```
4. **Install dependencies:**
    ```sh
    uv add "mcp[cli]" httpx
    ```

---

## 2. Building the Weather MCP Server

1. **Create `server.py` (or use `main.py`):**
    ```python
    from typing import Any
    import httpx
    from mcp.server.fastmcp import FastMCP

    # Initialize FastMCP server
    mcp = FastMCP("weather")

    # Constants
    OPENMETEO_API_BASE = "https://api.open-meteo.com/v1"
    USER_AGENT = "weather-app/1.0"

    # Helper function to make a request to the Open-Meteo API
    async def make_openmeteo_request(url: str) -> dict[str, Any] | None:
        headers = {
            "User-Agent": USER_AGENT,
            "Accept": "application/json"
        }
        async with httpx.AsyncClient() as client:
            try:
                response = await client.get(url, headers=headers, timeout=30.0)
                response.raise_for_status()
                return response.json()
            except Exception:
                return None

    @mcp.tool()
    async def get_current_weather(latitude: float, longitude: float) -> dict[str, Any] | str:
        """Get current weather for a location.

        Args:
            latitude: Latitude of the location
            longitude: Longitude of the location
        """
        url = f"{OPENMETEO_API_BASE}/forecast?latitude={latitude}&longitude={longitude}&current=temperature_2m,is_day,showers,cloud_cover,wind_speed_10m,wind_direction_10m,pressure_msl,snowfall,precipitation,relative_humidity_2m,apparent_temperature,rain,weather_code,surface_pressure,wind_gusts_10m"
        data = await make_openmeteo_request(url)
        if not data:
            return "Unable to fetch current weather data for this location."
        return data

    if __name__ == "__main__":
        mcp.run(transport='stdio')
    ```

---

## 3. Running the MCP Server

1. **Start the server in developer mode:**
    ```sh
    mcp dev server.py
    # or, if your file is named main.py:
    mcp dev main.py
    ```
2. **Open the MCP Inspector:**
    - Visit [http://localhost:5173](http://localhost:5173) in your browser.
    - Click **Connect**.
    - Go to the **Tools** tab.
    - Click **List Tools**.
    - Select `get_current_weather`.
    - Enter latitude and longitude (e.g., `40.7128, -74.0060` for New York City).
    - View the JSON weather data returned.

---

## 4. Using with Claude Desktop & Cursor

- **Claude Desktop:**
  - Open Claude Desktop and connect to your running MCP server.
  - Use the tool interface to call `get_current_weather` with desired coordinates.
- **Cursor:**
  - Open the project in Cursor.
  - Use the integrated terminal and editor to modify, run, and test your MCP server.

---

## 5. Extending the MCP Server

- Add more tools, e.g.:
  - `get_forecast`: Retrieve weather forecasts for a location.
  - `get_location`: Use Open-Meteo Geocoding API for location search.
- See the [Open-Meteo API documentation](https://open-meteo.com/en/docs) for more endpoints and options.

---

## 6. Troubleshooting

- Compare your code to the reference implementation if you encounter issues.
- Watch the "Troubleshooting MCP servers" video in your course materials.
- Consult the [official MCP documentation](https://docs.anthropic.com/claude/docs/mcp-overview) for debugging tips.

---

## 7. Tips

- Do not format the returned data in the tool; let the LLM process the raw JSON for best results.
- Use strong typing and docstrings for all tools.
- Use the MCP Inspector for rapid testing and debugging.

---

Happy hacking! 🚀 