# Changelog

This file documents all important changes to the exmcp project.

## [0.0.208] - 2026-10-26

### Requirements
- **Breaking Change**: This release of exmcp requires the ExtraHop system to be running firmware version 26.4 or later.
- **Breaking Change**: On macOS, this release of exmcp requires macOS 13 or later.

### Security

- The MCP server is now built with Go 1.27.3 to address the CVE-2026-111943 security vulnerability.

### Added

- The following tools were added to the MCP server:
    - `get_investigation`: Retrieves a specific investigation
    - `search_investigations`: Searches for investigations
    - `update_investigation`: Updates an investigation
    - `delete_investigation`: Deletes an investigation
- Improved security for Claude Desktop by adding the ability to install the MCP server with an MCP Bundle (MCPB) file. The MCPB enables you to store your REST API credentials in a credential store. Note that the MCPB file is currently unsigned.
- Added the `EXTRAHOP_PCAP_DOWNLOAD_DIRECTORY` environment variable, which specifies the directory to download packet capture files to.
- Added instructions to the README file for configuring Visual Studio Code with GitHub Copilot.

### Changed

- **Breaking Change**: The `search_detectionlogs` and `search_networkusers` tools now only search the last six hours by default. To modify the time range, you can specify the newly added `active_from` and `active_until` parameters.
- **Breaking Change**: The `search_detectionlogs` and `search_networkusers` tools no longer return the `total` field by default. To include the `total` field, specify the new `include_count` parameter.
- **Breaking Change**: The `search_detectionlogs` tool no longer returns the `has_more`, `next_offset`, and `clamped_limit` fields. To paginate results, specify the `limit` and `offset` parameters.
- **Breaking Change**: The `search_detectionlogs` tool now returns only 10 results by default. To paginate results, specify the `limit` and `offset` parameters.
* The `search_detectionlogs` and `search_networkusers` tools now return the `request_limit` field, which specifies the maximum number of results returned by the request. The `request_limit` is determined by the `limit` request parameter.
* The `search_detectionlogs` and `search_networkusers` tools now return the `time_range` field, which describes the time range queried by the request.
- The `search_devices` tool can now filter by and return the `lldp_name` field. The `get_device` tool also returns the `lldp_name` field.
- The `search_devices` tool now describes additional device roles, such as IoT, OT, and building management roles.
- The `get_eql_fieldvalues` tool now returns values for detection log fields.
- The exmcp repo now includes the binary files for the excli tool, which is also available from the agent-cli repo.
- The `update_detection` tool can now reopen a detection or clear its resolution, assignee, ticket ID, or AI disposition by specifying null.
- The `update_detection` tool can now clear ticket resolutions, assignees, ticket IDs, and AI dispositions by specifying null. Clearing a ticket resolution reopens the ticket.
- The MCP server no longer sends default values for parameters that the AI client omits.
- On macOS and Windows, if the `SSL_CERT_FILE` or `SSL_CERT_DIR` environment variable is set, the MCP server now trusts only the certificates that these variables specify instead of the system certificate store.
- Improved the stability and performance of the MCP server.

### Known Issues

- On RevealX 360, the `search_records` tool is unable to paginate results. If a query exceeds the maximum request limit, break the query into multiple queries with smaller time ranges.

## [0.0.161] - 2026-08-11

### Requirements
**Breaking Change**: This release of exmcp requires the ExtraHop system to be running firmware version 26.3 or later. If you are running an earlier firmware version, download the 0.0.120 release.

### Added

The following tools were added to the MCP server:

- `search_detectionlogs`: Searches for detection logs with the ExtraHop Query Language (EQL)
- `search_networkusers`: Searches for network users with EQL
- `get_eql_schema`: Retrieves EQL fields and operators
- `get_eql_fieldvalues`: Retrieves EQL field values
- `get_eql_syntax`: Retrieves EQL syntax
- `create_tuningrule`: Creates detection tuning rules
- `preview_tuningrule`: Previews the number of detection log entries that a tuning rule will hide
- `get_exmcp_version`: Retrieves the version of the exmcp server

### Changed

- **Breaking Change**: The `search_devices` and `search_detections` tools now return results directly in the response body, omit pagination metadata, and apply default limits of each REST operation.
- The MCP server metadata now includes the build version.
- The `search_records` tool now supports retrieving records by `detection_log_entry_id`.
- The `get_detection` tool now supports parsing additional response fields.
- The `search_detections` tool now supports parsing additional response fields, filtering by additional fields, sorting results, and retrieving only the ID of matching detections.

### Removed

- **Breaking Change**: The `-apikey` and `-clientsecret` command-line options have been removed. You must configure REST API keys and secrets through the `EXTRAHOP_API_KEY` and `EXTRAHOP_CLIENT_SECRET` variables.
- **Breaking Change**: The `search_detectionactivity` tool has been removed and replaced by the `search_detectionlogs` tool.

### Known Issues

- Claude Desktop Code and Claude Desktop Cowork are currently not compatible with several MCP tools. We recommend that you access the MCP server with Claude Desktop Chat (through the Chat toggle in Claude Desktop) or Claude Code until these issues are resolved.

## [0.0.120] - 2026-07-20

### Changed

- Disabled HTTP streaming. HTTP streaming will be re-enabled after OAuth support is available.
- The `create_investigation` tool now properly returns investigation IDs.

## [0.0.111] - 2026-07-13

### Changed

Updated build to Go version 1.26.5.

## [0.0.107] - 2026-05-29

### Added

- The following tools were added to the MCP server:
    - `get_extrahop_help_docs_url`: Retrieves the URL for the ExtraHop documentation website
    - `get_appliance_metadata`: Retrieves appliance metadata
- Added support for Linux ARM 64-bit machines
- Added MCP tool annotations with read-only hints
- Tool summaries are now exposed in MCP metadata
- Added the excli binary for invoking tools with JSON input and output
- Added support for localhost HTTP proxies

### Changed

- **Breaking Change**: Renamed environment variables to include the EXTRAHOP_ prefix.
- Increased the limit for records search.

## [0.0.89] - Initial Release

The initial release of the MCP server includes tools for interacting with the following resources through the ExtraHop REST API:

- Devices
- Device groups
- Detections
- Metrics
- Records
- Packets
- Investigations
- Tags
