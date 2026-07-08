# ExtraHop MCP Server

`exmcp` exposes ExtraHop data through the Model Context Protocol (MCP).

## **Important**: Recent Breaking Changes

- All environment variables have been renamed from EXMCP_* to EXTRAHOP_*.

## Verifying the Download

Each release includes a checksums file. Verify the integrity of your download
with SHA-256:

```bash
sha256sum -c exmcp_*_checksums.txt
```

### macOS Quarantine

After extracting the archive, macOS may quarantine the binary. After verifying
the download checksum, remove the quarantine attribute before use:

```bash
xattr -d com.apple.quarantine /path/to/exmcp
```

## Rx360 vs RxEnterprise

Configure exactly one credential family:

- Rx360: set `EXTRAHOP_CLIENT_ID` and `EXTRAHOP_CLIENT_SECRET`.
- RxEnterprise: set `EXTRAHOP_API_KEY`.

Configure credentials in your MCP client's config file (see the
client-specific sections below).

## Transport

The MCP server supports both stdio and HTTPS. To run in HTTPS mode, pass the `-streaming`, `-cert`, and `-key` flags.

## Access Control

The MCP server's access privilege is determined by the credentials it receives. The MCP server has no additional access controls at present.

In order to use the current tools, credentials require the following:
- NDR module access
- Read-only system access
- For write operations: full write system access
- For `download_pcap`: access to packets

## Packet Search (experimental)

The `download_pcap` tool is available only over stdio and only when
`-pcap-download-directory` is set. It writes PCAP files to the MCP server
filesystem.

It is strongly encouraged that `download_pcap` only be used in conditions where
the MCP client and MCP server are the same host and share the same file system.

## Codex CLI

Add `exmcp` to `~/.codex/config.json`:

```json
{
  "mcpServers": {
    "exmcp": {
      "type": "stdio",
      "command": "/path/to/exmcp",
      "args": ["tenant.cloud.extrahop.com"],
      "env": {
        "EXTRAHOP_CLIENT_ID": "...",
        "EXTRAHOP_CLIENT_SECRET": "..."
      }
    }
  }
}
```

Verify:

```bash
codex mcp list
```

## Claude Code / Claude Desktop

Add `exmcp` to one of the following config files:

- **Claude Code**: `.mcp.json` in your project directory
- **Claude Desktop**: `claude_desktop_config.json`

```json
{
  "mcpServers": {
    "exmcp": {
      "type": "stdio",
      "command": "/path/to/exmcp",
      "args": ["tenant.cloud.extrahop.com"],
      "env": {
        "EXTRAHOP_CLIENT_ID": "...",
        "EXTRAHOP_CLIENT_SECRET": "..."
      }
    }
  }
}
```

Verify in Claude Code / Claude Desktop:

```text
/mcp
```

## Known Issues

Claude Desktop Code and Claude Desktop Cowork currently have an
[issue](https://github.com/anthropics/claude-code/issues/58747) affecting
compatibility with several of the MCP tools in this project. Claude Desktop
users are encouraged to either use Claude Desktop Chat (via the Chat toggle in
Claude Desktop) or Claude Code until the issue is resolved.

## Tools

| Tool | Read/Write | Destructive | Description |
| --- | --- | --- | --- |
| `create_investigation` | Write | No | Create a new investigation to group related detections together for collaborative analysis. Investigations allow analysts to track and manage security incidents by associating detections, assigning ownership, and recording assessments and notes. |
| `search_detections` | Read | No | Search for security detections with filters like category, status, assignee, and type. Returns a compact summary for each detection. Use get_detection and search_detectionactivity for more details. |
| `get_detection` | Read | No | Get details for a detection by ID. Pair with search_detectionactivity for the correlated timeline. See search_detections tool description for documentation on how to pivot from participants to device metadata or records. |
| `update_detection` | Write | No | Update a detection. All fields are optional. Resolution is only valid when the detection status is (or is being set to) "closed". Setting status to a non-closed value clears any existing resolution. |
| `search_detectionactivity` | Read | No | Get the timeline of observed activity that makes up the detection, with correlated participants and properties. Use alongside get_detection when investigating a detection in detail. See search_detections tool description for documentation on how to pivot from participants to device metadata or records. |
| `get_detectiontypemetadata` | Read | No | Get metadata for a detection type by its type key. Returns the display name, MITRE ATT&CK technique IDs, categories, and typed property definitions. Only active detection formats and active properties are returned. Use to understand what a detection type means and what properties its activity entries will carry. |
| `get_appliance_metadata` | Read | No | Retrieve metadata about the firmware running on the ExtraHop appliance. |
| `get_extrahop_help_docs_url` | Read | No | Get the ExtraHop documentation URL for the connected appliance. |
| `search_devices` | Read | No | Search for devices with filters and active time range. Returns a compact summary for each device. To get full details for a device use get_device. |
| `get_device` | Read | No | Get full details for a device by ID aka OID. |
| `search_devicegroups` | Read | No | Perform a filtered search for collections of devices |
| `search_records` | Read | No | Search transaction records (HTTP, DNS, SSL, etc.). |
| `download_pcap` | Read | No | Download packets from the packet search endpoint to a local file in the configured pcap-download-directory. The filename is generated as a UUID. Streams the response body to that file and returns metadata. The consumer is responsible for cleaning up the pcap file as needed. |
| `search_devicetags` | Read | No | List all device tags on the system. Tags are user-defined labels that can be assigned to devices for grouping and filtering. |
| `list_devicetags_for_device` | Read | No | List tags assigned to a specific device. Tags are user-defined labels for grouping and filtering. |
| `list_devices_in_devicegroup` | Read | No | Retrieve all devices in the device group that were active within a specific time window. Returns a compact summary for each device. To get full details for a device use get_device. |
| `execute_metric_query` | Read | No | Query metrics (time series or total) for ExtraHop objects. |
| `search_metric_catalog` | Read | No | Search the local ExtraHop metric catalog. The catalog lists most builtin metrics that the appliance supports. Custom metrics are not included. |
| `assign_devicetag_to_devices` | Write | No | Assign a tag to one or more devices. The tag must already exist. |
| `unassign_devicetag_from_devices` | Write | No | Unassign a tag from one or more devices. The tag must already exist. |

## Related Repositories

This repository is a member of ExtraHop's agent repositories:

- ExtraHop/agent-mcp (this repository)
- [ExtraHop/agent-cli](https://github.com/ExtraHop/agent-cli)
- [ExtraHop/agent-skills](https://github.com/ExtraHop/agent-skills)
