# Getting AutoGen up & running in Docker

## Preface: set up Ooba Booga backend

1. First first, get Ooba Booga up and running in a Colab notebook. 
	1. https://colab.research.google.com/github/oobabooga/text-generation-webui/blob/main/Colab-TextGen-GPU.ipynb#scrollTo=LGQ8BiMuXMDG
	2. Run the first cell, which will display an audio player. Don't worry, it's just 24 hours of silence, which is golden. Hit play to ensure that Colab doesn't idle in the background.
	3. Before running the second cell, change `model_url` to the model you'd like to use.
		1. I have been using this one as a default: https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2
		2. Set `branch` to `main` or keep it empty. Depending on the model you choose, something else might belong here.
		3. Tick the box next to `api` to expose an OpenAI-like API.
	4. When you run the second cell, it will load the model and begin to run it. When it's ready, it will print a URL ending in `.trycloudflare.com` - copy this link.
	5. Paste it into `./test/test.py` on line 11, as the value for `config_list["base_url"]`. Make sure to add `/v1` to the end - no trailing slash!
	6. When you're done, your `config_list` should look something like this:

```python
config_list = [
    {
        "model": "mistralai/Mistral-7B-Instruct-v0.2", #the name of your running model
        # https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2
        "base_url": "https://bedding-ict-casa-comparative.trycloudflare.com/v1", #the local address of the api
        # base_url modified from api_base
        "api_type": "open_ai",
        "api_key": "sk-111111111111111111111111111111111111111111111111", # just a placeholder
    }
]
```

7. Note: if you are using an open source model (such as the one linked above), the value of the `api_key` field is irrelevant. If you plan on hooking this up to OpenAI, you'll need to provide your OpenAI key.

## Build a Docker image containing AutoGen + dependencies

1. First, build an image from ./dev/Dockerfile.
    - This Dockerfile is modified from the dev Dockerfile on the AutoGen repository.
    - As of 2024-01-25, the original can be found here: https://github.com/microsoft/autogen/blob/main/.devcontainer/dev/Dockerfile.
    - This will take a while - it is almost 10GB.
    - Note: I had better chances of building successfully when I disabled my VPN.
    - Run the following command from the root folder of this repository:

```bash
docker build -t autogen_dev_img:latest dev
```

## Run a container with the sample scripts inside

1. If you have any files or scripts that you'd like to access from within Docker, add them to the `/import/scripts` directory.
2. Next, run the following command. This will build a new image that imports files from `/import/scripts` and provide a service that lets you access files in both images.

```bash
docker compose up --build autogen_test
```

3. Note: if you want to change any of the scripts, you will need to re-run this command and rebuild the image `autogen_test`. Because we are using `docker compose`, you won't have to rebuild the huge 10GB image, just the custom files.
4. Next, to run a container interactively in the terminal, use the following command.

```bash
docker compose run -it autogen_test /bin/bash
```

5. If you've made any edits to the `compose.yaml` file, you may need to change `autogen_test` to match the name of the service. Use this command if you need to look up the name of the current services:

```bash
docker compose config --services
```

6. When you're done with the container, run `docker compose down` to clear the old container and services.

## Running the sample script

1. Once you're inside the container, navigate to the directory `/home/autogen/autogen/import`. This is where the test scripts are imported by default, but this can be reconfigured by changing the `COPY` command in `import/Dockerfile`.
2. There are two test scripts included - run one of the following commands to execute the script:

```bash
python test.py
python test_with_input.py
```

3. `test.py` has a hard-coded API endpoint (which you edited in the first part of this walkthrough), as well as a hard-coded instruction message. It will write the current date and time to `./paper/datetime.txt`.
4. Use `test_with_input.py` if you'd like to enter a new API endpoint and/or new instruction prompt without rebuilding the current Docker image.

Note: If you're running a script of multiple agents, try disabling lines 243-249 in groupchat.py from autogen/contrib. It's a check for running one agent, but it'll bog speed and consume more tokens with a multi-agent script if the check is left active. The "failed to resolve next speaker" dialogue can be resolved by disabling it.
