# Changelog

All notable changes to this project will be documented in this file.

## [0.0.111] - 2026-07-13

### Changed

- Update Go build to 1.26.5

## [0.0.107] - 2026-05-29

### Added

- Linux arm64 support
- ExtraHop help docs URL tool (`get_extrahop_help_docs_url`)
- Appliance metadata tool (`get_appliance_metadata`)
- MCP tool annotations with read-only hints
- Tool summaries exposed in MCP metadata
- `excli` binary for invoking tools with JSON input/output
- Localhost HTTP proxy support

### Changed

- **Breaking:** Rename environment variables to `EXTRAHOP_` prefix
- Raise records search limit cap

## [0.0.89] - Initial Release

- MCP server with ExtraHop REST API tools
- Device, detection, metric, record, and packet capture tools
- Device group and tag management tools
- Investigation tools
- Metric catalog search
