````markdown
# Grafana MCP Local POC - Managing grafana with Claude Desktop or ChatGPT's Codex

This project sets up a local Grafana and Prometheus environment and connects it to desktop MCP clients such as Claude Desktop and Codex / ChatGPT Desktop using the `grafana/mcp-grafana` Docker image.

This setup is intended for local desktop use only. The MCP server runs locally through Docker, and the desktop application config files are updated on the local machine.

## Prerequisites

Make sure you have the following installed:

- Docker
- Docker Compose
- Claude Desktop, if configuring Claude
- Codex / ChatGPT Desktop, if configuring Codex

## Start The Local Stack

Start the containers:


```bash
docker-compose up -d
```

## Validate The Services

After the containers start, validate that the services are reachable via browser.

Prometheus:

```text
</>
http://localhost:9090/query
```

Grafana:

```text
<http://localhost:3000>
```

## Create A Grafana Service Account

Log in to Grafana and create a service account.

Then create an access token for that service account.

Save the token value. It will be used in the MCP configuration as:

```text
TOKEN_VALUE_CREATED_IN_GRAFANA_UI
```

## Pull The Grafana MCP Image

Pull the Grafana MCP Docker image locally:

```bash
docker pull grafana/mcp-grafana
```

This is required so Claude Desktop or Codex can start the MCP server container when needed.

When the desktop clients start the MCP server, you can verify the running container with:

```bash
docker ps
```

## Configure MCP For Desktop Clients

The MCP server is configured locally in each desktop client.

This setup does not run a remote MCP server and will not work from a browser-only environment. The configuration files must be updated locally for Claude Desktop and Codex / ChatGPT Desktop.

Before editing the config files, quit any open desktop apps:

- Claude Desktop
- Codex / ChatGPT Desktop

After updating the config files, reopen the apps so they load the new MCP server configuration.

## Codex Configuration

Edit the Codex config file:

```bash
vi ~/.codex/config.toml
```

Add the following configuration:

```toml
[mcp_servers.grafana]
command = "docker"
args = ["run", "--rm", "-i", "-e", "GRAFANA_URL", "-e", "GRAFANA_SERVICE_ACCOUNT_TOKEN", "grafana/mcp-grafana", "-t", "stdio"]

[mcp_servers.grafana.env]
GRAFANA_URL = "http://host.docker.internal:3000"
GRAFANA_SERVICE_ACCOUNT_TOKEN = "TOKEN_VALUE_CREATED_IN_GRAFANA_UI"
```

Replace:

```text
TOKEN_VALUE_CREATED_IN_GRAFANA_UI
```

with the access token created in Grafana.

## Claude Desktop Configuration

Edit the Claude Desktop config file:

```bash
vi ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

Add or update the `mcpServers` configuration:

```json
{
  "mcpServers": {
    "grafana": {
      "command": "/usr/local/bin/docker",
      "args": [
        "run",
        "--rm",
        "-i",
        "-e",
        "GRAFANA_URL",
        "-e",
        "GRAFANA_SERVICE_ACCOUNT_TOKEN",
        "grafana/mcp-grafana",
        "-t",
        "stdio"
      ],
      "env": {
        "GRAFANA_URL": "http://host.docker.internal:3000",
        "GRAFANA_SERVICE_ACCOUNT_TOKEN": "TOKEN_VALUE_CREATED_IN_GRAFANA_UI"
      }
    }
  }
}
```

Replace:

```text
TOKEN_VALUE_CREATED_IN_GRAFANA_UI
```

with the access token created in Grafana.

## Validate Claude Desktop JSON

Claude Desktop uses a JSON config file, so the file must be valid JSON.

Validate it with:

```bash
python3 -m json.tool ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

If the command prints formatted JSON without errors, the config file is valid.

## Useful Docker Commands

Pull images:

```bash
docker-compose pull
```

Start containers in detached mode:

```bash
docker-compose up -d
```

View logs:

```bash
docker-compose logs
```

Stop and remove containers, networks, and volumes:

```bash
docker-compose down -v
```

Use this command when you want to completely clean the project state.

## ChatGPT / Codex MCP Reference

Documentation for connecting MCP servers from ChatGPT Desktop:

[https://learn.chatgpt.com/docs/extend/mcp?surface=app](https://learn.chatgpt.com/docs/extend/mcp?surface=app)
````