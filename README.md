
# Getting AutoGen up & running in Docker

(still a work in progress, but the script runs!)

## Helpful links

- https://microsoft.github.io/autogen/
- https://github.com/microsoft/autogen

## Changes made to autogen repo's Dockerfile.dev

1. Okay, so I was building an image from autogen/samples/dockers/Dockerfile.dev
	1. now it's at 1. autogen/.devcontainer/dev/Dockerfile
		1. (I had to modify it a bit to autogen/samples/dockers/Dockerfile.ping.dev)
		2. Added these lines before the big `apt-get install` below
```docker
RUN apt-get install -y iputils-ping
RUN ping -c 4 deb.debian.org
```

3. I had to split the line `RUN apt-get install -y sudo git npm vim nano curl` into multiple commands (not sure if that fixed it but I think that made a difference)
```docker
RUN apt-get install -y curl
RUN apt-get install -y sudo
RUN apt-get install -y git
RUN apt-get install -y npm
RUN apt-get install -y vim
RUN apt-get install -y nano
```


## Build the image from the new Dockerfile.test.dev

```bash
docker build -f Dockerfile.ping.dev -t autogen_dev_img .
docker build -f Dockerfile.test -t autogen_test_img .
```

## Running a container from the image, mounting volume

```bash
docker run -it -v test_volume:/test --name test_autogen_container autogen_test_img
docker run -it --name test_autogen_container autogen_test_img
```


docker run -it -v $(pwd)/autogen:/app autogen_dev_img
docker run -it -v test_volume:/aFolder --name test_autogen_container test_autogen.dev
## CD into /home/autogen & run setup.py 

```bash
sudo python setup.py build
sudo python setup.py install
```