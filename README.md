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

## Configuring Text Generation Web UI

You need to begin by downloading a large language model in the "Models" tab.

Enter the following details in the download box on the right hand side, then hit the big "Download" button to grab the model.

Download Model: [TheBloke/Mistral-7B-OpenOrca-GGUF](https://huggingface.co/TheBloke/Mistral-7B-OpenOrca-GGUF)
File: mistral-7b-openorca.Q4_K_M.gguf

> Mistral 7B is an exceptional model for local use. It has fast inference times and does not need very much system memory (8GB).

This download will usually take several minutes.

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

## OpenAI API Documentation

* [OpenAI API Reference](https://platform.openai.com/docs/api-reference)
* [OpenAI API in OpenAPI format](https://github.com/openai/openai-openapi/blob/master/openapi.yaml)
