# Generative AI In Backstage | Ben Wilcock @ BackstageCon-2023

Thanks for taking the time to see [my talk at BackstageCon 2023](https://colocatedeventsna2023.sched.com/event/07a22acf572c9ba6ac78a3fff50c6e7d). This folder represents my notes and other setup instructions necessary to recreate what I built for the demonstration of [Backchat - The AI Testbed For Backstage](https://github.com/benwilcock/backstage-plugin-backchat).

[Contact me on LinkedIn](https://www.linkedin.com/in/benwilcock/)
[Watch My BackstageCon Talk On GenAI](https://youtu.be/YuEsB4YQGUY?si=F9SCM0QudTv8rvo7)

## BackstageCon 2023 Talk - **GenAI In Backstage - Turbocharge Your Developer Productivity!**

Backstage provides a powerful developer platform, but integrating the latest AI capabilities can take it to the next level. In this talk, we'll explore how to leverage large language models like ChatGPT within Backstage to automate routine tasks, generate code, find answers, and boost developer productivity. In this talk, you’ll learn how to use off-the-shelf open-source components to add a ChatGPT facility into Backstage, switch between different large language models, and prompt the LLM to deliver better responses.

## The "Backchat" Plugin

At BackstageCon 2023 I introduced the "Backchat" plugin - a generative AI testbed for Backstage.  The AI servers and GUIs required by the plugin can be started using the instructions in this repo. To get the Backchat plugin and to integrate it with your Backstage instance, see the [backstage-plugin-backchat](https://github.com/benwilcock/backstage-plugin-backchat) repository on GitHub.

<figure><img src="./docs/images/backchat-architecture.jpg" alt="The Backchat Plugin Architecture" width="640px"><figcaption>The Backchat Plugin Architecture.</figcaption></figure>

## Launching The LLM Servers and GUI's Locally

Once you have cloned this repo locally, you can bring up the background AI servers and GUIs for Backchat using the `docker-compose.yaml` fule (requires [docker compose](https://docker.com)).

```bash
git clone https://github.com/benwilcock/backstagecon-2023.git
cd backstagecon-2023
docker compose up
```

## Notes On This Setup

There are many docker containers in this setup (which allows you to test out multiple frontend GUIs and backend LLM servers with [Backchat](https://github.com/benwilcock/backstage-plugin-backchat).

* The [LocalAI](https://localai.io) container provides a backend LLM server for use with frontends like Chatbot UI.
* The [Chatbot UI](https://github.com/mckaywrigley/chatbot-ui) container provides frontend LLM chat client for use with backends like LocalAI.
* The [Ollama Web UI](https://github.com/ollama-webui/ollama-webui) container provides a frontend chat client for the [Ollama](https://github.com/jmorganca/ollama) container backend. This combo is possibly the easiest to use.
* The [Text Generation Web UI](https://github.com/oobabooga/text-generation-webui)  provides both an LLM backend and frontend client in a single container. It is possible to also use Chatbot UI with the backend it provides.
* The [Big-AGI UI](https://github.com/enricoros/big-agi) is a nice looking and easy to use web frontend that can talk to multiple backends including Ollama, Text Gen Web UI (Oobabooga) and LocalAI at the same time. 

the LocalAI, Text Generation Web UI, and Ollama LLM servers all have the ability to download LLMs. LocalAI and Text Gen Web UI provide an OpenAI compatible API for use with various frontends and other components. Chatbot UI can be configured to use either LocalAI or Text Generation Web UI as its backend. The Ollama combo is probably the easiest to use for setting up models, but I'm not sure of it's compatibility with other clients (so bear that in mind).

> If you know exactly which client/server combination you want to use, you can safely comment out the containers you do not need in the `docker-compose.yaml` file (the # symbol at the start of a line comments out the whole line).

## LocalAI & Chatbot UI (the default choice)

LocalAI should work as a backend right out of the box, so long as you gave it time to download a default model. Chatbot UI is also configured to use this server by default. Simply being patient and running the `docker compose` should be sufficient to give you access to your very own local (and private) LLM.

> If you have issues, check the docker logs. A browser call to the LocalAI server endpoint can be used to test that the OpenAI API server is working correctly on [http://localhost:8080/v1/models](http://localhost:8080/v1/models)

There are more detailed instructions for configuring ChatBotUI's server preference below.

## Configuring Ollama Web UI / Ollama

This GUI/server combination probably offers the easiest model setup of all. Once you have spun up the servers using the `docker compose` command above, open the [Ollama Web UI](http://localhost:3100) in your browser. Before you can chat you must add a model. To add a model, click the 'Settings' icon (a small cog wheel) next to the model chooser. When the settings panel appears, choose the 'Models' tab and in the "Pull a model" box type the name of the model you would like to use (e.g. `mistral`). Click the green download button icon, and the model will be downloaded for you. The downloader will show the progress of your download. This can take several minutes depending on your bandwidth. Once the model is downloaded, simply select it from the model chooser or the main chat screen. You can now send prompts to your model.

> Mistral 7B is an exceptional model for local use. It has fast inference times, generates good text, and does not need very much system memory (around 8-16GB for the Q4 version model).

## Configuring Text Generation Web UI

Generally I find this server to offer the fastest inference times. Before you begin, setup a large language model by downloading one using the "Models" tab in the [Text Generation WebUI GUI](http://localhost:7860).

Enter the following details in the download box on the right hand side, then hit the big "Download" button to grab the model. This download will usually take several minutes.

| **GUI Form Box** | **Setting**                                                                                                                           |
|------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| Model Name:      | [TheBloke/Mistral-7B-OpenOrca-GGUF](https://huggingface.co/TheBloke/Mistral-7B-OpenOrca-GGUF)                                         |
| Filename:        | [mistral-7b-openorca.Q4_K_M.gguf](https://huggingface.co/TheBloke/Mistral-7B-OpenOrca-GGUF/blob/main/mistral-7b-openorca.Q4_K_M.gguf) |
 
> Mistral 7B is an exceptional model for local use. It has fast inference times, generates good text, and does not need very much system memory (around 8GB for the Q4 version model).

Once downloaded, reboot the container and go back to Models page of the [Text Generation WebUI GUI](http://localhost:7860) and now 'select' the `mistral-7b-openorca.Q4_K_M.gguf` as your model and click the "Load" button. Set the model to use as many threads as you have physical CPUs (if your PC supports 8 threads, you probably have 4 CPUs, so set it to "4").

Finally, head to the "Chat" tab and ask a question. For example:

```text
You: "Who was the president of the United States in 1997?"
```

The AI should respond with a reasonable answer, such as:

```text
The president of the United States in 1997 was Bill Clinton.
```

If you encounter issues, check the docker logs. You can test the API server is working executing a `GET` request on the [http://localhost:5001/v1/models](http://localhost:5001/v1/models) endpont.The server should return a list of the available models.

## Switching Chatbot UI's Backend

In the configuration for the Chatbot UI container (in the `docker-compose.yaml` file), set the `OPENAI_API_HOST` environment variable to point to the endpoint of the backend server you'd like to use.

To use the LocalAI server as the OpenAI API backend:

```yaml
...
environment:
  - 'OPENAI_API_HOST=http://local-ai-api:8080' # LocalAI API server
...
```

To use Text Generation Web UI server (with the `openai` extension loaded) as the OpenAI API backend:

```yaml
...
environment:
  - 'OPENAI_API_HOST=http://text-generation-webui:5001' # Text Generation UI OpenAI API server
...
```

## Configuring Big-AGI UI To talk to Ollama, LocalAI, or Text Gen Web UI

Big-AGI is very flexible. It can be configured to talk to multiple backends at the same time. To configure a backend, in the Big-AGI GUI in your browser, choose the "Models" dropdown and choose "Models" (or press `ctrl-shift-M`). In the popup, choose to "Add +" a model server. From the list of server types, choose either "LocalAI" or "Ollama" or "Oobabooga" (Text Gen Web UI) as required. 

> You can only add one server at a time in this window but you can use them all interchangably once you have set them up. 

You will then be prompted for the URL of the API server you want to use. Don't for get to use the Docker DNS names and exposed ports assigned in the `docker-compose.yaml` in your URL setting. For example, "http://ollama:11434" would be the correct URL for connections to the Ollama server.

## Integrating Any Of These UIs With Backstage

To use either of these LLM Chat GUIs in backstage, add the [Backchat plugin for Backstage](https://github.com/benwilcock/backstage-plugin-backchat) to your setup and configure `app-config.local.yaml` with the URL of the GUI interface you'd like to incorporate and test in Backstage. See [app-config.local.yaml.sample](./app-config.local.yaml.sample) in the root of this repo for an example of this file.

## Adding The Backchat TechDocs And Catalog To Backstage

To add a full Backchat system hierarchy and the TechDocs used in this talk to your Backstage instance, simply register the following URL into your Backstage software catalog. You can do this through your Backstage UI, or by adding the following configuration to the catalog section of your backstage instance's `app-config.local.yaml` file.

[https://github.com/benwilcock/backstagecon-2023/blob/main/backchat-catalog.yaml](https://github.com/benwilcock/backstagecon-2023/blob/main/backchat-catalog.yaml)

```yaml
catalog:
  locations:
    - type: url
      target: https://github.com/benwilcock/backstagecon-2023/blob/main/backchat-catalog.yaml
  rules:
    - allow: [Component, API, Resource, System, Domain, Location, Group, User] # User and Group must be added.
```

## Full Server & Client List

| **App** (*=default) | **Web Frontend** | **LLM Backend** | **Mapped PORT** | **Docker Image** | **Open Source (Lic)** | **OpenAI API** | **Notes** |
|---|---|---|---|---|---|---|---|
| Chatbot UI* | Yes | No | [3001](http://localhost:3001) | Yes | Yes (MIT) | Yes | Development stopped. Bug: Expects model name to be "gpt-3.5-turbo" |
| Big-AGI | Yes | No | [3456](http:localhost:3456) | Yes | Yes (MIT) | Yes | Requires initial setup of LocalAI or Ollama or Text Gen Web UI servers in prefs. |
| LocalAI* | No | Yes | 8080 | Yes | Yes (MIT) | Yes | Auto loads model on demand. Can download models on boot. Model download & install mechanism is a bit funky. |
| Text Gen Web UI | Yes | Yes | [7860](http://localhost:7860)/5001 | Yes | Yes (GNU AFFERO) | Yes (via extension) | Quite complex. Lots of features. Used by TheBloke. Feels pretty fast when models are pre-loaded. |
| Ollama Web UI | Yes | No | [3100](http://localhost:3100) | Yes | Yes (MIT) | ?? No |  |
| Ollama | No | Yes | 11434 | Yes | Yes (MIT) | ?? No | Seems to offer a non-standard API, but Ollama Web UI and Big-AGI can talk to it. |
| ChatGPT-web | Yes | No | 5173 | DIY | Yes (GPL3) | ?? No | Claims to be OpenAI API compatible, but didn't work for me when testing with LocalAI and LM Studio servers. |
| OpenVino Model Server | No | Yes | 9000 | Yes | Yes (Apache2) | ?? No | ML. Does it even do LLM? |
| LM Studio (Desktop) | No | Yes | 1234 | No | No | Yes | Great for desktop backend testing without Docker. Easy model download. Lots of settings. Linux is BETA only. API backend hasn't worked that well with clients. Perhaps best used to test models. |

## Links For The Switchable Frontend GUIs

Ports shown here are those exposed by he components in the `docker-compose.yaml` file.

* [Chatbot UI on localhost port 3001](http://localhost:3001)
* [Text Generation Web UI on localhost port 7860](http://localhost:7860)
* [Ollama Web UI on localhost port 3100](http://localhost:3100)
* [Big-AGI UI on localhost port 3456](http://localhost:3456)
* [Backstage GUI on localhost port 3000](http://localhost:3000) (not part of `docker-compose.yaml`)

## OpenAI API Documentation

* [OpenAI API Reference](https://platform.openai.com/docs/api-reference)
* [OpenAI API in OpenAPI format](https://github.com/openai/openai-openapi/blob/master/openapi.yaml)
