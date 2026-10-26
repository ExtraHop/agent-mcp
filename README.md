# ExtraHop MCP Server

The ExtraHop MCP Server (`exmcp`) exposes ExtraHop data through the Model Context Protocol (MCP). The MCP server enables agents to access ExtraHop data so that you can search for threats, metrics, and records.

## System requirements
The ExtraHop system must be running firmware version 26.4 or later. If you are running firmware version 26.3, download the 0.0.161 release of the MCP server. If you are running an earlier firmware version, download the 0.0.120 release of the MCP server.

On macOS, the MCP server requires macOS 13 or later.

## Install the MCP server

### Retrieve the MCP server executable file

1. If you do not already have a directory for your MCP project files, create the directory. For example, run the following command:
    `mkdir mcp-test`
2. Clone this GitHub repo. For example:
    `git clone https://github.com/ExtraHop/agent-mcp.git`
3. Verify the integrity of the download with the checksums file. For example:
    `cd agent-mcp/dist/`
    `sha256sum -c *_checksums.txt`
    `cd ../..`
4. Copy the appropriate installation file for your machine to your MCP directory. For example:
    `cp agent-mcp/dist/exmcp-linux-amd64-{build-number}.tar.gz mcp-test/.`
5. If the installation file is a TAR file, extract the executable file from the TAR file and then remove the TAR file. For example:
    `tar -xvf mcp-test/exmcp-linux-amd64-{build-number}.tar.gz -C mcp-test/.`
    `rm mcp-test/exmcp-linux-amd64-{build-number}.tar.gz`
6. For Mac machines, remove the exmcp or exmcp.mcpb file from quarantine. For example:
    `xattr -d com.apple.quarantine mcp-test/exmcp`
    `xattr -d com.apple.quarantine mcp-test/exmcp-{build-number}.mcpb`

#### MCP installation files

| Operating System | Filename |
| --- | --- |
| Linux AMD/Intel x86_64 | exmcp-linux-amd64-{build-number}.tar.gz |
| Linux ARM 64-bit | exmcp-linux-arm64-{build-number}.tar.gz |
| macOS Intel x86_64 | exmcp-darwin-amd64-{build-number}.tar.gz |
| macOS ARM 64-bit | exmcp-darwin-arm64-{build-number}.tar.gz |
| Windows | exmcp-windows-amd64-{build-number}.exe |
| Windows, macOS, and Linux (Claude Desktop only) | exmcp-{build-number}.mcpb |

### Configure your AI client to run the ExtraHop MCP server

For your AI client to run the MCP server, you must configure the client to provide the hostname of the console and valid REST API credentials to the server at runtime. The procedure for this depends on the AI client that you are running. The sections below include procedures for some of the most common AI clients.

#### Generate REST API credentials

The MCP server must have valid REST API credentials to access your ExtraHop system. Which actions the MCP server can perform are determined by the privileges granted to the credentials. We recommend that you grant the API credentials the lowest level of privilege necessary to perform the tasks you want the MCP server to perform.

The following table describes which permissions are required to enable each action.

| Permission | Required For |
| --- | --- |
| NDR module access | All MCP deployments |
| Full read-only privileges or higher | All MCP deployments |
| Full write privileges or higher | Managing device tags, managing detection tuning rules, creating investigations, updating detection ticket tracking info |
| Packets only access or higher | Downloading packets |

1. Generate the credentials.
    1. For ExtraHop sensors and RevealX Enterprise consoles, see [Generate an API key](https://docs.extrahop.com/current/rest-api-guide/#generate-an-api-key).
    2. For RevealX 360 consoles, see [Create REST API credentials](https://docs.extrahop.com/current/rx360-rest-api/#create-rest-api-credentials).

**Note**: For a complete list of actions that the MCP server can perform, see [Tools](#tools).

#### Configure Codex CLI

1. Add your REST API key or secret to an environment variable. Treat these environment variables as secrets and protect them according to security best practices, such as storing the environment variables in a keyring.
    1. For RevealX 360, add the secret of your REST API credentials to the EXTRAHOP_CLIENT_SECRET environment variable.
    2. For RevealX Enterprise consoles and sensors, add your REST API key to the EXTRAHOP_API_KEY environment variable.
2. In the MCP project directory, create a .codex directory. For example:
    `mkdir .codex`
3. In a text editor, create the file .codex/config.toml. For example:
    `vi .codex/config.toml`
4. For RevealX 360, add the following TOML table to the file and replace the specified variables:
    - `<console-hostname>`: The hostname of the RevealX 360 console.
    - `<client-id>`: The ID of the [RevealX 360 REST API credentials](https://docs.extrahop.com/current/rx360-rest-api/#create-rest-api-credentials).
    ```
    [mcp_servers.exmcp]
    command = "./exmcp"
    args = ["<console-hostname>"]
    env_vars = ["EXTRAHOP_CLIENT_SECRET"]

    [mcp_servers.exmcp.env]
    EXTRAHOP_CLIENT_ID = "<client-id>"
    ```
5. For RevealX Enterprise, add the following TOML table to the file and replace the specified variables:
    - `<console-hostname>`: The hostname of the RevealX Enterprise console.
    ```
    [mcp_servers.exmcp]
    command = "./exmcp"
    args = ["<console-hostname>"]
    env_vars = ["EXTRAHOP_API_KEY"]
    ```
6. (Optional) Enable packet capture downloads by specifying the EXTRAHOP_PCAP_DOWNLOAD_DIRECTORY environment variable. Add the variable to the TOML table under mcp_servers.exmcp.env. Specify the path of the local directory to save downloaded packet capture files to.
    ```
    EXTRAHOP_PCAP_DOWNLOAD_DIRECTORY = "<path-to-directory>"
    ```
    For more information about packet capture downloads, see the MCP Tool Capabilities section.
7. Run the following command to verify that the configuration was successful:
    `codex mcp list`

#### Configure Claude Code

1. Add your REST API key or secret to an environment variable. Treat these environment variables as secrets and protect them according to security best practices, such as storing the environment variables in a keyring.
    1. For RevealX 360, add the secret of your REST API credentials to the EXTRAHOP_CLIENT_SECRET environment variable.
    2. For RevealX Enterprise consoles and sensors, add your REST API key to the EXTRAHOP_API_KEY environment variable.
2. In the MCP project directory, create the .mcp.json file. For example:
    `vi .mcp.json`
3. For RevealX 360, add the following JSON object to the file and replace the specified variables:
    - `<console-hostname>`: The hostname of the RevealX 360 console.
    - `<client-id>`: The ID of the [RevealX 360 REST API credentials](https://docs.extrahop.com/current/rx360-rest-api/#create-rest-api-credentials).
    ```
    {
      "mcpServers": {
        "exmcp": {
          "type": "stdio",
          "command": "./exmcp",
          "args": ["<console-hostname>"],
          "env": {
            "EXTRAHOP_CLIENT_ID": "<client-id>",
            "EXTRAHOP_CLIENT_SECRET": "${EXTRAHOP_CLIENT_SECRET}"
          }
        }
      }
    }
    ```
4. For RevealX Enterprise, add the following JSON object to the file and replace the specified variables:
    - `<console-hostname>`: The hostname of the RevealX Enterprise console.
    ```
    {
      "mcpServers": {
        "exmcp": {
          "type": "stdio",
          "command": "./exmcp",
          "args": ["<console-hostname>"],
          "env": {
            "EXTRAHOP_API_KEY": "${EXTRAHOP_API_KEY}"
          }
        }
      }
    }
    ```
5. (Optional) Enable packet capture downloads by specifying the EXTRAHOP_PCAP_DOWNLOAD_DIRECTORY environment variable. Add the variable to the JSON file to the env object under mcpServers.exmcp. Specify the path of the local directory to save downloaded packet capture files to.
    ```
      "EXTRAHOP_PCAP_DOWNLOAD_DIRECTORY": "<path-to-directory>"
    ```
    For more information about packet capture downloads, see the MCP Tool Capabilities section.
6. Run the following command to start Claude:
    `claude`
7. Run the following command to verify that the configuration was successful:
    `/mcp`

#### Configure Claude Desktop

1. Open Claude Desktop, and then open the **Settings** menu.
2. Select **Extensions** > **Advanced settings** > **Install Extension**.
3. Select the `exmcp-{build-number}.mcpb` file.
4. Click **Install**.
5. On the Configure ExtraHop MCP Server page, enter the following information:
    - `ExtraHop hostname`: The hostname of the RevealX 360 or RevealX Enterprise console.
    - `Client ID`: (RevealX 360 only) The ID of your REST API credentials. For RevealX Enterprise, leave this field blank.
    - `Client secret`: (RevealX 360 only) The secret of your REST API credentials. For RevealX Enterprise, leave this field blank.
    - `API key`: (RevealX Enterprise) Your API key. For RevealX 360, leave this field blank.
    - `PCAP download directory`: (Optional) The path of the local directory to save downloaded packet capture files to. If you do not want to enable packet capture downloads, leave this field blank.
6. Click **Save**.
    **Note**: Claude Desktop displays a warning because the MCPB is currently unsigned and is considered a custom extension.
7. If the extension is displayed as Disabled, click **Enable**.
8. Open a new chat window, and click **Connectors**.
9. Verify that `ExtraHop MCP Server` appears in the **Connectors** list.

#### Configure Visual Studio Code with GitHub Copilot

1. In the MCP project directory, create a `.vscode` directory. For example:
    `mkdir .vscode`
2. In a text editor, create the file `.vscode/mcp.json`. For example:
    `vi .vscode/mcp.json`
3. For RevealX 360, add the following JSON object to the file and replace the specified variables:
    - `<console-hostname>`: The hostname of the RevealX 360 console.
    - `<client-id>`: The ID of the [RevealX 360 REST API credentials](https://docs.extrahop.com/current/rx360-rest-api/#create-rest-api-credentials).
    ```
    {
      "inputs": [
        {
          "type": "promptString",
          "id": "extrahop-client-secret",
          "description": "ExtraHop Client Secret",
          "password": true
        }
      ],
      "servers": {
        "exmcp": {
          "type": "stdio",
          "command": "./exmcp",
          "args": ["<console-hostname>"],
          "env": {
            "EXTRAHOP_CLIENT_ID": "<client-id>",
            "EXTRAHOP_CLIENT_SECRET": "${input:extrahop-client-secret}"
          }
        }
      }
    }
    ```
4. For RevealX Enterprise, add the following JSON object to the file and replace the specified variables:
    - `<console-hostname>`: The hostname of the RevealX Enterprise console.
    ```
    {
      "inputs": [
        {
          "type": "promptString",
          "id": "extrahop-api-key",
          "description": "ExtraHop API Key",
          "password": true
        }
      ],
      "servers": {
        "exmcp": {
          "type": "stdio",
          "command": "./exmcp",
          "args": ["<console-hostname>"],
          "env": {
            "EXTRAHOP_API_KEY": "${input:extrahop-api-key}"
          }
        }
      }
    }
    ```
5. (Optional) Enable packet capture downloads by specifying the EXTRAHOP_PCAP_DOWNLOAD_DIRECTORY environment variable. Add the variable to the JSON file to the env object under servers.exmcp. Specify the path of the local directory to save downloaded packet capture files to.
    ```
      "EXTRAHOP_PCAP_DOWNLOAD_DIRECTORY": "<path-to-directory>"
    ```
    For more information about packet capture downloads, see the MCP Tool Capabilities section.
6. Open Visual Studio Code.
7. Open the **Command Palette**.
8. Type `MCP: List Servers` and then press ENTER.
9. Select **exmcp** from the drop-down menu.
10. Select **Start Server** from the drop-down menu.
11. For RevealX 360, in the **ExtraHop Client Secret** field, paste the secret of your REST API Credentials and then press ENTER.
    The Output pane displays `Connection state: Running`
12. For RevealX Enterprise, in the **ExtraHop API Key** field, paste your REST API key and then press ENTER.
    The Output pane displays `Connection state: Running`

## MCP Tool Capabilities

The MCP server tools enable AI clients to perform the following actions.

**Detections**
Retrieve detections, manage detection tuning rules, and update detection participant and ticket tracking info. All detection and detection activity log information is surfaced to the agent, including participant identifiers and activity timestamps. Tuning rules can be created but not deleted.

**Records**
Retrieve records. All record fields are surfaced to the agent, including fields for custom records.  Requires either RevealX 360 with Premium Investigation or RevealX Enterprise with an EXA 5300, EXA 5300v, BigQuery, or LogScale recordstore.

**Metrics**
Retrieve metrics. All metric information is surfaced to the agent, including the metric catalog, which lists each built-in metric on the ExtraHop system.

**Packets**
Download packet capture (PCAP) files. All stored packets can be downloaded by the agent. However, the agent cannot initiate new packet captures. Note that an agent might be able to reconstruct files transferred over the network from downloaded packets. However, the agent cannot [extract files through the ExtraHop REST API](https://docs.extrahop.com/current/rest-extract-files/).

The MCP server can only download packets if the server is communicating with your client over standard input and output (stdio). The MCP server writes PCAP files to the filesystem of the server. We strongly recommend that you enable packet capture downloads only if the AI client is running on the same machine as the MCP server or the client has access to the MCP server filesystem.

**Devices**
Retrieve devices, retrieve device groups, and manage device tags. All device information is surfaced to the agent, including IP and MAC addresses, domain names, and device make and model information. Device groups can be viewed but not modified or deleted. Tags can be assigned or removed from devices, but new tags cannot be created.

**Investigations**
Retrieve and manage investigations. The agent can create, update, and delete investigations.

**Network Users**
Retrieve network users. All available information about the network user is surfaced to the agent, including devices that the user was observed on.

**Firmware Metadata**
Retrieve metadata about firmware running on the ExtraHop appliance. The agent cannot upgrade or rollback appliance firmware.

**Documentation**
Retrieve the URL of the public [ExtraHop documentation site](https://docs.extrahop.com/current/).

**MCP Metadata**
Retrieve the version of the MCP server that is currently running.

**EQL Syntax**
Retrieve ExtraHop Query Language (EQL) syntax to support search functionality.

## Tools

| Tool | Read/Write | Destructive | Description |
| --- | --- | --- | --- |
| `get_exmcp_version` | Read | No | Get the version of the running ExtraHop MCP server. |
| `create_investigation` | Write | No | Create a new investigation to group related detections together for collaborative analysis. Investigations allow analysts to track and manage security incidents by associating detections, assigning ownership, and recording assessments and notes. Returns the ID of the new investigation. |
| `update_detection` | Write | Yes | Update a detection. All fields are optional. Resolution is only valid when the detection status is (or is being set to) "closed". Setting status to a non-closed value clears any existing resolution. |
| `get_detectiontypemetadata` | Read | No | Get metadata for a detection type by its type key. Returns the display name, MITRE ATT&CK technique IDs, categories, and typed property definitions. Only active detection formats and active properties are returned. Use to understand what a detection type means and what properties its activity entries will carry. |
| `get_appliance_metadata` | Read | No | Retrieve metadata about the firmware running on the ExtraHop appliance. |
| `get_extrahop_help_docs_url` | Read | No | Get the ExtraHop documentation URL for the connected appliance. |
| `create_tuningrule` | Write | Yes | Create a tuning rule. |
| `delete_investigation` | Write | Yes | Delete a specific investigation. |
| `get_detection` | Read | No | Retrieve a specific detection. |
| `get_investigation` | Read | No | Retrieve a specific investigation. |
| `preview_tuningrule` | Read | No | Retrieve a preview that describes how many detection log entries will be hidden by a tuning rule. |
| `search_detectionlogs` | Read | No | Search detection log entries. |
| `search_detections` | Read | No | Search for detections. |
| `search_devices` | Read | No | Retrieve all active devices that match specific criteria. |
| `search_investigations` | Read | No | Search for investigations. |
| `search_networkusers` | Read | No | Search for network users. |
| `update_investigation` | Write | Yes | Update an investigation. |
| `get_device` | Read | No | Get full details for a device by ID aka OID. |
| `search_devicegroups` | Read | No | Perform a filtered search for collections of devices. |
| `search_records` | Read | No | Search transaction records (HTTP, DNS, SSL, etc.). |
| `download_pcap` | Read | No | Download packets from the packet search endpoint to a local file in the server's configured PCAP download directory. The filename is generated as a UUID. Streams the response body to that file and returns metadata. The consumer is responsible for cleaning up the pcap file as needed. If the server has no PCAP download directory configured, the tool returns an error and packets cannot be downloaded. |
| `search_devicetags` | Read | No | List all device tags on the system. Tags are user-defined labels that can be assigned to devices for grouping and filtering. |
| `list_devicetags_for_device` | Read | No | List tags assigned to a specific device. Tags are user-defined labels for grouping and filtering. |
| `list_devices_in_devicegroup` | Read | No | Retrieve all devices in the device group that were active within a specific time window. Returns a compact summary for each device. To get full details for a device use get_device. |
| `execute_metric_query` | Read | No | Query metrics (time series or total) for ExtraHop objects. |
| `search_metric_catalog` | Read | No | Search the local ExtraHop metric catalog. The catalog lists most builtin metrics that the appliance supports. Custom metrics are not included. |
| `assign_devicetag_to_devices` | Write | No | Assign a tag to one or more devices. The tag must already exist. |
| `unassign_devicetag_from_devices` | Write | No | Unassign a tag from one or more devices. The tag must already exist. |
| `get_eql_syntax` | Read | No | Get the EQL (ExtraHop Query Language) query syntax reference shared by every EQL search tool. |
| `get_eql_schema` | Read | No | Get the EQL search schema for a resource: the fields that can be queried and the operators available. |
| `get_eql_fieldvalues` | Read | No | Get the expected values for one or more EQL fields on a resource. |

## Command-line interface (excli)

The repo also includes a command-line interface for interacting with the toolset of the MCP server. The `excli` command accepts JSON input and returns JSON output. The command-line interface enables shell piping and terminal workflows.

### Install the command-line interface

1. If you do not already have a directory for your ExtraHop project files, create the directory. For example, run the following command:
    `mkdir extrahop-test`
2. Clone this GitHub repo. For example:
    `git clone https://github.com/ExtraHop/agent-mcp.git`
3. Verify the integrity of the download with the checksums file. For example:
    `cd agent-mcp/dist/`
    `sha256sum -c *_checksums.txt`
    `cd ../..`
4. Copy the appropriate installation file for your machine to your ExtraHop project directory. For example:
    `cp agent-mcp/dist/excli-darwin-amd64-{build-number}.tar.gz extrahop-test/.`
5. If the installation file is a TAR file, extract the executable file from the TAR file and then remove the TAR file. For example:
    `tar -xvf extrahop-test/excli-darwin-amd64-{build-number}.tar.gz -C extrahop-test/.`
    `rm extrahop-test/excli-darwin-amd64-{build-number}.tar.gz`
6. For Mac machines, remove the `excli` file from quarantine. For example:
    `xattr -d com.apple.quarantine extrahop-test/excli`
7. Add your REST API credentials to environment variables. Treat these environment variables as secrets and protect them according to security best practices, such as storing the environment variables in a keyring.
    1. For RevealX 360, add the following environment variables:
        - `EXTRAHOP_CLIENT_ID`:  The ID of your REST API credentials.
        - `EXTRAHOP_CLIENT_SECRET`:  The secret of your REST API credentials.
    2. For RevealX Enterprise consoles and sensors, add your REST API key to the `EXTRAHOP_API_KEY` environment variable.
8. Add the hostname of your console to the `EXTRAHOP_HOST` environment variable.
9. Enter your project directory. For example:
    `cd extrahop-test`
10. Run the following command to confirm that the installation was successful.
    `./excli -help`
    The command-line documentation appears.

### Run the excli command

1. Run the excli in the following format (the `-json` option is optional):
    `./excli <tool> [-json <json>]`
    Replace the following variables:
    - `<tool>`: The name of an MCP tool. You can view a list of all MCP tools by running `./excli -listtools`.
    - `<json>`: Parameters for the MCP tool, in JSON format.

### Configuration options
You can set the following environment variables to customize your excli installation.

- `EXTRAHOP_INSECURE`: **Boolean**
    Indicates whether to bypass TLS certificate validation for the RevealX Enterprise console or a proxy service for the RevealX 360 console. The default value is false. You can specify true to bypass certificate validation. However, this method is not secure and is not recommended.
- `EXTRAHOP_PCAP_DOWNLOAD_DIRECTORY`: **String**
    The path of the local directory to save downloaded packet capture files to. This option is required to enable packet capture downloads.

## Related Repository

- [ExtraHop/agent-skills](https://github.com/ExtraHop/agent-skills)
