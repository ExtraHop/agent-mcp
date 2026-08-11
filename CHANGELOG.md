# Changelog

All notable changes to this project will be documented in this file.

## [0.0.161] - 2026-08-11

**Requires ExtraHop firmware 26.3 or later. For earlier firmware versions, see the [0.0.120 release](https://github.com/ExtraHop/agent-mcp/tree/a383721e39500e2621185dc1ec52e5635ee31306/dist).**

### Added

- EQL detection log search (`search_detectionlogs`)
- EQL network user search (`search_networkusers`)
- EQL schema discovery (`get_eql_schema`)
- EQL field value discovery (`get_eql_fieldvalues`)
- EQL syntax discovery (`get_eql_syntax`)
- Detection tuning rule creation (`create_tuningrule`)
- Detection tuning rule preview (`preview_tuningrule`)
- exmcp version discovery (`get_exmcp_version`)

### Changed

- Report the build version in MCP server metadata
- `search_records` supports retrieving records by `detection_log_entry_id`
- `get_detection` supports additional response fields
- `search_detections` supports additional response fields, filters, sorting, and
  ID-only results
- **Breaking:** `search_devices` and `search_detections` return REST results
  under `body`, omit pagination metadata, and use REST API default limits
- ExtraHop Firmware version 26.3 or later is now required.

### Removed

- **Breaking:** Remove the `exmcp` `-apikey` and `-clientsecret` command-line
  options; use `EXTRAHOP_API_KEY` and `EXTRAHOP_CLIENT_SECRET` instead
- **Breaking:** Remove `search_detectionactivity`; use `search_detectionlogs` instead

### Known Issues

- Calling `search_records` with an unsupported `detection_log_entry_id` returns
  a 500 error.

## [0.0.120] - 2026-07-20

### Changed

- Disable HTTP streaming until OAuth support is available
- `create_investigation` properly returns investigation ID

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
