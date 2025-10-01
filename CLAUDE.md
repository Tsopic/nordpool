# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a Home Assistant custom integration for Nord Pool, providing spot market electricity prices for Nordic, Baltic, and parts of Western Europe. The integration fetches electricity prices from the Nord Pool API (supporting both 15-minute and hourly periods) and exposes them as sensors with rich attributes for use in automations and dashboards.

**As of October 1, 2025**, the Nord Pool API now provides 15-minute interval data instead of hourly data. This integration supports both period types with automatic detection.

## Development Setup

### Using DevContainer (Recommended)
1. Open the repository in VS Code
2. Select "Reopen in Container" when prompted
3. Run task "Run Home Assistant on port 9123" to start a test instance
4. Configuration is in `.devcontainer/configuration.yaml`

### Manual Setup
```bash
# Install dependencies
./scripts/setup

# Run development instance
./scripts/dev
```

This creates a `config/` directory and starts Home Assistant with the custom component loaded via `PYTHONPATH`.

## Architecture

### Core Components

**`__init__.py`** - Integration setup and data coordinator
- `NordpoolData`: Central data store managing price data for all currencies/areas
- Fetches data at key times:
  - **New day (00:00)**: Rolls over tomorrow's data to today
  - **New period (every 15 minutes)**: Updates current price and requests new data if needed
  - **New price (13:10-13:30 CET)**: Fetches tomorrow's prices (Nord Pool publishes ~13:00 CET)
- Uses dispatcher events (`EVENT_NEW_DAY`, `EVENT_NEW_HOUR`, `EVENT_NEW_PRICE`) to notify sensors
- Random minute/second offsets prevent thundering herd when many sensors are configured
- Period callback fires every 15 minutes (at :00, :15, :30, :45) to support both hourly and 15-minute periods

**`sensor.py`** - Sensor entity implementation
- `NordpoolSensor`: Main sensor entity exposing current price and attributes
- Calculates price with VAT, additional costs (via templates), and unit conversions (kWh/MWh/Wh, cents)
- Provides attributes: `today`, `tomorrow`, `average`, `min`, `max`, `peak`, `off_peak_1`, `off_peak_2`, `low_price`, `period_type`
- Template support in `additional_costs`: Access `current_price` and `now()` for dynamic cost calculations
- **Auto-detects period type** from API response (15-minute or hourly) and adjusts calculations accordingly
- Configurable `period_type` preference (defaults to `15min`), but actual period is detected from API data

**`aio_price.py`** - API client
- `AioPrices`: Async HTTP client for Nord Pool Data Portal API
- Fetches 3 days of data (yesterday, today, tomorrow) to handle timezone differences
- `join_result_for_correct_time()`: Critical function that filters API results to include only hours in the sensor's local timezone
- Handles DST transitions, invalid values (inf, None), and currency validation

**`config_flow.py`** - UI configuration
- Validates region codes and additional cost templates
- Note: UI-configured sensors cannot be reconfigured without deletion/recreation

**`services.py`** - Service call handlers
- Exposes `nordpool.hourly`, `nordpool.yearly`, etc. for fetching raw API data
- Useful for automations needing historical or future data

### Data Flow

1. Sensor requests data from `NordpoolData` for its area/currency
2. `NordpoolData` calls `AioPrices.hourly()` if data not cached
3. `AioPrices` fetches 3 days from API: `https://dataportal-api.nordpoolgroup.com/api/DayAheadPrices`
4. `join_result_for_correct_time()` filters results to local timezone hours
5. Sensor receives list of `{start, end, value}` dicts and calculates prices with VAT/costs
6. Three scheduled callbacks keep data fresh (new day, new hour, new price at 13:00 CET)

### Important Constants (`const.py`)

- `_REGIONS`: Maps region codes (NO1, SE3, DK1, etc.) to [currency, country, VAT]
- `tzs`: Maps region codes to IANA timezones (critical for correct period filtering)
- `RANDOM_MINUTE`/`RANDOM_SECOND`: Randomized fetch times to distribute API load
- `INVALID_VALUES`: Values that should raise `InvalidValueException`
- `PERIOD_HOURLY`: Constant for hourly periods (`"hour"`)
- `PERIOD_15MIN`: Constant for 15-minute periods (`"15min"`)
- `DEFAULT_PERIOD_TYPE`: Default period type (currently `PERIOD_15MIN`)

## Testing & Debugging

### Enable Debug Logging
Add to Home Assistant `configuration.yaml`:
```yaml
logger:
  logs:
    nordpool: debug
    custom_components.nordpool: debug
    custom_components.nordpool.sensor: debug
    custom_components.nordpool.aio_price: debug
```

### Common Issues
- **"No data for tomorrow"**: Normal before 13:00 CET; API hasn't published next day
- **Missing hours**: Check timezone mapping in `tzs` for the region
- **Template errors**: Validate `additional_costs` template renders to float
- **DST transitions**: 23 or 25 hour days are expected; handled by `join_result_for_correct_time()`

## Key Implementation Details

### Period Type Support (New in 2025)
As of October 1, 2025, Nord Pool transitioned to 15-minute intervals:
- **Configuration**: Users can set `period_type` to `"15min"` (default) or `"hour"`
- **Auto-detection**: The sensor automatically detects actual period length from API response
- **Adaptive calculations**: Peak/off-peak calculations adjust based on detected period count (24 for hourly, 96 for 15-minute)
- **Update frequency**: Callbacks fire every 15 minutes to support fine-grained price updates
- **Backward compatible**: Existing configurations continue to work; the integration handles both period types transparently

### Timezone Handling
The integration deals with three time contexts:
1. **API returns UTC** timestamps
2. **Users expect local time** in their region (Stockholm for SE, Oslo for NO, etc.)
3. **Tomorrow's prices publish at 13:00 CET** (Stockholm timezone)

The `join_result_for_correct_time()` function in `aio_price.py` is critical - it filters API results to only include periods that fall within a calendar day in the sensor's local timezone.

### Template Additional Costs
The `additional_costs` parameter accepts Jinja2 templates with special variables:
- `current_price`: Base price before additional costs (includes VAT if enabled)
- `now()`: Returns the datetime of the price hour, not actual current time

This allows time-based tariffs like peak/off-peak pricing.

### Backoff on Missing Data
The `@backoff.on_exception` decorator on `new_data_cb()` in `__init__.py` retries fetching tomorrow's prices if they're not available yet (using backoff library). It tries every 10 minutes for up to 2 hours after 13:00 CET.

### Period Detection and Adaptation
The `_update_current_price()` method in `sensor.py` automatically detects the period type from API response:
- Calculates period length from first entry: `(end - start).total_seconds() / 60`
- If ≤15 minutes: sets `_detected_period_type` to `PERIOD_15MIN`
- Otherwise: sets to `PERIOD_HOURLY`
- All time matching and calculations use the detected period type
- Peak/off-peak calculations adapt to period count: `periods_per_hour = len(data) // 24`

## Region Codes & API

Nord Pool covers regions with specific codes:
- **Norway**: NO1 (Oslo), NO2 (Kristiansand), NO3 (Trondheim), NO4 (Tromsø), NO5 (Bergen)
- **Sweden**: SE1-SE4
- **Denmark**: DK1, DK2
- **Finland**: FI
- **Baltics**: EE, LT, LV
- **Western Europe**: AT, BE, DE-LU, FR, NL

See region map: https://data.nordpoolgroup.com/map

## File Structure
```
custom_components/nordpool/
├── __init__.py           # Integration setup, data coordinator, event scheduling
├── sensor.py             # Sensor entity with price calculations and attributes
├── aio_price.py          # API client with timezone-aware data fetching
├── config_flow.py        # UI configuration flow
├── services.py           # Service call handlers (hourly, yearly, etc.)
├── const.py              # Constants, regions, timezones, VAT rates
├── events.py             # Custom event tracking utilities
├── misc.py               # Helper functions
└── manifest.json         # Integration metadata
```
