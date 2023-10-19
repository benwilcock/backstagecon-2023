# backstagecon-2023
BackstageCon 2023

## Launching The LLM Servers and UI's

You can bring up the background AI servers and GUIs with:

```bash
docker compose up -d
```

You can tail the logs to see the status of things with:

```bash
docker compose logs --follow
```

## Notes On This Setup

There are three servers in this setup. 

* [LocalAI](https://localai.io) (provides backend only)
* [Chatbot UI](https://github.com/mckaywrigley/chatbot-ui) (provides frontend only)
* [Text Generation Web UI](https://github.com/oobabooga/text-generation-webui) (provides both backend and frontend)

Both LocalAI and Text Generation Web UI have the ability to download LLMs and provide an OpenAI API for compatibility with various frontends. Chatbot UI can be configured to use either LocalAI or Text Generation Web UI as its backend.

## Configuring The Chatbot UI Backend

See  for hints on how to do this.

In the configuration for the Chatbot UI container (in the `docker-compose.yaml` file), set the `OPENAI_API_HOST` environment variable to point to the endpoint of the backend server you'd like to use.

For LocalAI server:

```yaml
...
environment:
  - 'OPENAI_API_HOST=http://local-ai-api:8080' # LocalAI API server
...
```

For Text Generation Web UI server (with the `openai` extension loaded):

```yaml
...
environment:
  - 'OPENAI_API_HOST=http://text-generation-webui:5001' # Text Generation UI OpenAI API server
...
```

## Links for the GUIs

* [Chatbot UI on localhost port 3001](http://localhost:3001)
* [Text Generation Web UI on localhost port 7860](http://localhost:7860)

## Integration With Backstage

To use either UI in backstage, add the [Backchat plugin for Backstage](https://github.com/benwilcock/backstage-plugin-backchat) to your setup and configure `app-config.local.yaml` with the URL of the GUI you'd like to incorporate.
