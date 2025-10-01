# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.0.18] - 2025-10-01

### Added
- **15-minute period support** - Integration now supports Nord Pool's 15-minute interval pricing (transitioned from hourly on October 1, 2025)
- **Automatic period detection** - Sensor automatically detects period type (15min or hourly) from API response
- **`period_type` configuration** - New optional configuration parameter to set period preference (defaults to `15min`)
- **`period_type` sensor attribute** - New attribute showing the detected period type
- **Adaptive calculations** - Peak/off-peak calculations now adapt dynamically to period granularity (24 hourly vs 96 15-minute periods)

### Changed
- **Update frequency** - Callbacks now fire every 15 minutes (at :00, :15, :30, :45) instead of hourly for more timely price updates
- **Default period type** - New installations default to 15-minute periods
- **Buy Me a Coffee link** - Updated to new URL

### Fixed
- Time rounding functions now support both hourly and 15-minute boundaries
- Peak/off-peak calculations correctly handle variable period counts
- Tomorrow validation adapts to expected period count (23+ for hourly, 92+ for 15-minute)

### Technical
- Enhanced `start_of()` and `end_of()` functions in `misc.py` with 15-minute support
- Added `PERIOD_HOURLY` and `PERIOD_15MIN` constants in `const.py`
- Implemented period length detection: `(end - start).total_seconds() / 60`
- Updated `extract_attrs()` to calculate `periods_per_hour = len(data) // 24`

### Backward Compatibility
- ✅ Fully backward compatible with existing configurations
- ✅ Handles both hourly and 15-minute API responses
- ✅ No breaking changes to sensor attributes or behavior

## [0.0.17] - 2024-XX-XX

### Previous Release
- Prior version maintained by original maintainers
- For history before 0.0.17, see git commit history

[Unreleased]: https://github.com/Tsopic/nordpool/compare/v0.0.18...HEAD
[0.0.18]: https://github.com/Tsopic/nordpool/compare/v0.0.17...v0.0.18
[0.0.17]: https://github.com/Tsopic/nordpool/releases/tag/v0.0.17
