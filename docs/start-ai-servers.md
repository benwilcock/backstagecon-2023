Once you have cloned [this repo](https://github.com/benwilcock/backstage-plugin-backchat) locally, you can bring up the background AI servers and GUIs for Backchat using Docker compose (requires [Docker](https://docker.com)).

```bash
git clone https://github.com/benwilcock/backstage-plugin-backchat.git
cd backstage-plugin-backchat
docker compose up
```

## Notes On This Setup

There are three containers in this setup. 

* [LocalAI](https://localai.io) (provides a backend AI server only)
* [Chatbot UI](https://github.com/mckaywrigley/chatbot-ui) (provides frontend AI client only)
* [Text Generation Web UI](https://github.com/oobabooga/text-generation-webui) (provides both an AI backend and frontend)

Both LocalAI and Text Generation Web UI have the ability to download LLMs and provide an OpenAI API for compatibility with various front ends. Chatbot UI can be configured to use either LocalAI or Text Generation Web UI as its backend.

## Configuring LocalAI

LocalAI should work as a backend right out of the box, so long as you gave it time to download a default model.

If you already loaded the Backchat software catalog YAML into your Backstage instance, you can also test the OpenAI API provided by this server via the API definition page in Backstage. Start by choosing the server `http://localhost:8080` from the list and execute a `GET` on the `/Models` endpoint. There's no need to authenticate. The server should return a list of the available models.

> In theory this should work but I had issues (a browser call to the same address does work [http://localhost:8080/v1/models](http://localhost:8080/v1/models))

## Configuring Text Generation Web UI

You need to begin by downloading a large language model in the "Models" tab in the [Text Generation WebUI GUI](http://localhost:7860).

Enter the following details in the download box on the right hand side, then hit the big "Download" button to grab the model.

| **GUI Form Box** | **Setting**                                                                                                                           |
|------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| Model Name:      | [TheBloke/Mistral-7B-OpenOrca-GGUF](https://huggingface.co/TheBloke/Mistral-7B-OpenOrca-GGUF)                                         |
| Filename:        | [mistral-7b-openorca.Q4_K_M.gguf](https://huggingface.co/TheBloke/Mistral-7B-OpenOrca-GGUF/blob/main/mistral-7b-openorca.Q4_K_M.gguf) |
 

> Mistral 7B is an exceptional model for local use. It has fast inference times, generates good text, and does not need very much system memory (around 8GB for the Q4 version model).

This download will usually take several minutes.

Once downloaded, reboot the container and go back to Models page of the [Text Generation WebUI GUI](http://localhost:7860) and now 'select' the `mistral-7b-openorca.Q4_K_M.gguf` as your model and click the "Load" button. Set the model to use as many threads as you have physical CPUs (if your PC supports 8 threads, you probably have 4 CPUs, so set it to "4").

Finally, head to the "Chat" tab and ask the Backchat AI (A.K.A the "Assistant") a question. For example

```text
You: "Who was the president of the United States in 1997?"
```

The AI should respond with a reasonable answer, such as:

```text
The president of the United States in 1997 was Bill Clinton.
```

If you already loaded the Backchat software catalog YAML into your Backstage instance, you can also try the OpenAI API calls via the API definition page in Backstage. Start by using the server `http://localhost:5001` and execute a `GET` on the `/Models` endpoint. There's no need to authenticate. The server should return a list of the available models.

## Configuring The Chatbot UI Backend

In the configuration for the Chatbot UI container (in the `docker-compose.yaml` file), set the `OPENAI_API_HOST` environment variable to point to the endpoint of the backend server you'd like to use.

To use LocalAI server as the backend API:

```yaml
...
environment:
  - 'OPENAI_API_HOST=http://local-ai-api:8080' # LocalAI API server
...
```

To use Text Generation Web UI server (with the `openai` extension loaded) as the backend API:

```yaml
...
environment:
  - 'OPENAI_API_HOST=http://text-generation-webui:5001' # Text Generation UI OpenAI API server
...
```

## Integrating These AIs With Backstage

To use either of these AI GUIs in backstage, add the [Backchat plugin for Backstage](https://github.com/benwilcock/backstage-plugin-backchat) to your setup and configure `app-config.local.yaml` with the URL of the GUI you'd like to incorporate (instructions are provided in the repo).
