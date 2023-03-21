---
comment: Flow tested by building own image from Dockerfile housed in SA GitHub NOT tested from publically-available image
title: Docker
author: HB and Signing Agent team's docs
proofedDate: na
---


## TL;DR

- Create a Docker volume
- Create a Docker container
- Run a Docker volume that references your configuration file

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed
- [Docker image](https://hub.docker.com/r/qredohub/signing-agent) downloaded.

### Create a Docker volume for storing config data

You need to create a local folder to store the config.yaml file and also the agent database. When you run the Docker image, you must reference this local folder since it is shared between the local system and the Docker image. For this reason, we need to create the Docker volume first. If you are curious, you can go here to learn more about [Docker volumes](https://docs.docker.com/storage/volumes/).

1. Create a local folder and name it `volume`.

1. With Docker running, create the Docker volume (please use full path) in your:

	`docker volume create --name volume_folder --opt type=none --opt device={path-to-your-volume-folder}/volume --opt o=bind`

### Configure your data

A [config.yaml is provided](https://github.com/qredo/signing-agent/blob/master/config-template.yaml). Review the [configuration documentation](configure) to determine which settings you wish to apply.

### Run the Signing Agent image

**Important!** Before running the Docker image, please make sure to copy the `config.yaml` (YAML configuration) file to your local `volume`.

1. Import the Docker image you obtained from Qredo:

	`docker load < {path-to-your-volume-folder}/image.tar`

1. Run the Docker image in the terminal, using the command:

	`docker run -p 8007:8007 -v volume_folder:/volume signing-agent:dev`


## What next?

You can now test and deploy the Docker image. 

{% callout %}
When running as a Docker container, a host directory must be mounted as a virtual volume: in order to ensure data persistency between different container versions.
{% /callout %}
