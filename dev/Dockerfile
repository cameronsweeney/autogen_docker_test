# This Dockerfile is modified from the dev Dockerfile on the AutoGen repository.
# https://github.com/microsoft/autogen/blob/main/.devcontainer/dev/Dockerfile
# These changes fixed some fatal build errors I kept getting on Windows.

# Basic setup
FROM python:3.11-slim-bookworm

# Update and install necessary packages
RUN apt-get update && apt-get -y update

# For some reason, I had trouble connecting to the debian server with apt-get
# I tried to ping the server to troubleshoot the network error & test if I could connect to the internet at all.
# I don't know why, but I can't build this image on my machine when I remove the following commands - maybe you don't need them, but it may solve a weird error for you.
RUN apt-get install -y iputils-ping
RUN ping -c 4 deb.debian.org

# I also had trouble installing all of the following packages with just one command. Splitting them into separate commands increased my chances of building successfully.
RUN apt-get install -y curl
RUN apt-get install -y sudo
RUN apt-get install -y git
RUN apt-get install -y npm
RUN apt-get install -y vim
RUN apt-get install -y nano

# Setup a non-root user 'autogen' with sudo access
RUN adduser --disabled-password --gecos '' autogen
RUN adduser autogen sudo
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
USER autogen
WORKDIR /home/autogen

# Set environment variable
# ENV OPENAI_API_KEY="{OpenAI-API-Key}"

# Clone the AutoGen repository
RUN git clone https://github.com/microsoft/autogen.git /home/autogen/autogen
WORKDIR /home/autogen/autogen

# Install AutoGen in editable mode with extra components
RUN sudo pip install -e .[test,teachable,lmm,retrievechat,mathchat,blendsearch]

# Install pre-commit hooks
RUN pre-commit install

# Setup Docusaurus and Yarn for the documentation website
RUN sudo npm install --global yarn
RUN sudo pip install pydoc-markdown
RUN cd website
RUN yarn install --frozen-lockfile --ignore-engines

# Exposes the Yarn port for Docusaurus
EXPOSE 3000

# Pre-load popular Python packages
RUN pip install numpy pandas matplotlib seaborn scikit-learn requests urllib3 nltk pillow pytest beautifulsoup4

# Set the default command to bash
CMD ["/bin/bash"]
