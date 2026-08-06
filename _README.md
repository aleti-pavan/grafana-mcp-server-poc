


1. docker compose up

validate all the urls 
1. prometheus : 
2. grafana 

3. login into grafana and create a service account and then a access token. 

4. make sure we pull the graana-mcp imae with docker pull so it's available on the system so claude or chatgpt/codex can actually run when we instruct it to. 

`docker pull grafana/mcp-grafana`
5. Then configure mcp servers for both claude and codex platforms. 
Note: we are doing it for local platforms and not remove as this is being run locally in the laptop. This wouldn't work with browser as the mcp isn't running remote and it's running local and we are updating the config files locally for the codes and claude desktop applications. 

Config files to update 
Claude:   vi ~/Library/Application Support/Claude/claude_desktop_config.json
Codex:   vi ~/.codex/config.toml

Codex```
[mcp_servers.grafana]
command = "docker"
args = ["run", "--rm", "-i", "-e", "GRAFANA_URL", "-e", "GRAFANA_SERVICE_ACCOUNT_TOKEN", "grafana/mcp-grafana", "-t", "stdio"]

[mcp_servers.grafana.env]
GRAFANA_URL = "http://host.docker.internal:3000"
GRAFANA_SERVICE_ACCOUNT_TOKEN = "TOKEN_VALUE_CREATED_IN_GRAFANA_UI" 
```

Claude```

"mcpServers": {
        "grafana": {
            "command": "/usr/local/bin/docker",
            "args": [
                "run", "--rm", "-i",
                "-e", "GRAFANA_URL",
                "-e", "GRAFANA_SERVICE_ACCOUNT_TOKEN",
                "grafana/mcp-grafana",
                "-t", "stdio"
            ],
            "env": {
                "GRAFANA_URL": "http://host.docker.internal:3000",
                "GRAFANA_SERVICE_ACCOUNT_TOKEN": "TOKEN_VALUE_CREATED_IN_GRAFANA_UI"
            }
        }
    }
```

Note: make sure you validate the config file for claude as it's a json file the config file has to be valid. You can use following command to validate the same.

command: `python3 -m json.tool ~/Library/Application\ Support/Claude/claude_desktop_config.json`





docker-compose down -v - if you want completely clean the project state. 

docker-compose pull  - pull images we do it first time
docker-compose up -d - run the app and in the background i.e detached mode
docker-compose logs - for seeing logs

make sure you run docker pull grafana/mcp-grafana so it pulls the image on the your mac , and when you query claude or codex they will run their own mcp servers
which you can see with docker ps 



how to connect to mcp servers from codex/chatgpt from desktop app.

https://learn.chatgpt.com/docs/extend/mcp?surface=app


files : 

Claude:   vi ~/Library/Application Support/Claude/claude_desktop_config.json
 Codex:   vi ~/.codex/config.toml

always quit if you open these apps( codex, claude desktop ) before updating the files, or after updating the config files so when you open next time it picks up the fresh config which contains your mcp server config. 

claude was straight forward and chatgpt was bit a struggle initially to configure. 

