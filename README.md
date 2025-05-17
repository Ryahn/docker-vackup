# Vackup: Backup and Restore Docker Volumes

[![DevSkim](https://github.com/Ryahn/docker-vackup/actions/workflows/devskim.yml/badge.svg)](https://github.com/Ryahn/docker-vackup/actions/workflows/devskim.yml) [![Lint Code Base](https://github.com/Ryahn/docker-vackup/actions/workflows/super-linter.yml/badge.svg)](https://github.com/Ryahn/docker-vackup/actions/workflows/super-linter.yml)

**This is now an [Official Docker Desktop Extension called "Volumes Backup & Share"](https://hub.docker.com/extensions/docker/volumes-backup-extension) which has more features, but I'll keep this repo around for historial purposes.**

Vackup: (contraction of "volume backup")

Easily backup and restore Docker volumes using either tarballs or container images.
It's designed for running from any host/container where you have the docker CLI.

Note that for open files like databases,
it's usually better to use their preferred backup tool to create a backup file,
but if you stored that file on a Docker volume,
this could still be a way you get the Docker volume into a image or tarball
for moving to remote storage for safe keeping.

`export`/`import` commands copy files between a local tarball and a volume.
For making volume backups and restores.

`save`/`load` commands copy files between an image and a volume.
For when you want to use image registries as a way to push/pull volume data.

Usage:

`vackup export VOLUME FILE`
  Creates a gzip'ed tarball in current directory from a volume

`vackup import FILE VOLUME`
  Extracts a gzip'ed tarball into a volume

`vackup save VOLUME IMAGE`
  Copies the volume contents to a busybox image in the /volume-data directory

`vackup load IMAGE VOLUME`
  Copies /volume-data contents from an image to a volume

`vackup backup-container CONTAINER DIR`
  Backs up the configuration and volumes of a container to a directory
  If no container is specified, backs up all non-blacklisted containers

`vackup backup-all DIR`
  Backs up all non-blacklisted containers to a directory

`vackup restore-container DIR CONTAINER`
  Restores the configuration and volumes of a container from a directory

## Container Blacklist

You can exclude specific containers from being backed up by adding them to the `BLACKLISTED_CONTAINERS` array in the script. For example:

```bash
BLACKLISTED_CONTAINERS=(
    "container1"
    "container2"
)
```

## Install

Download the `vackup` file in this repository to your local machine in your shell path and make it executable.

```shell
sudo curl -sSL https://raw.githubusercontent.com/Ryahn/docker-vackup/refs/heads/main/vackup -o /usr/local/bin/vackup && sudo chmod +x /usr/local/bin/vackup
```

## Error conditions

If any of the commands fail, the script will check to see if a `VACKUP_FAILURE_SCRIPT`
environment variable is set.  If so it will run it and pass the line number the error
happened on and the exit code from the failed command.  Eg,

```shell
# /opt/bin/vackup-failed.sh
LINE_NUMBER=$1
EXIT_CODE=$2
send_slack_webhook "Vackup failed on line number ${LINE_NUMBER} with exit code ${EXIT_CODE}!"
```

```shell
export VACKUP_FAILURE_SCRIPT=/opt/bin/vackup-failed.sh
./vackup export ......
```
