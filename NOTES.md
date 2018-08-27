# Docker Notes

## Documentation

- [Docker Docs](https://docs.docker.com/reference/)
- Passing dashes in between multi-parameter commands will open manual pages for that specific command.

  - `man docker-network-create`

## Dockerfile Directives

[Dockerfile Reference](https://docs.docker.com/engine/reference/builder/#stopsignal)

- **ADD**

  - _PURPOSE_: Copies files from _src_ and adds them to the filesystem's _**IMAGE**_ @ _dest_
  - _dest_ is an absolute path, or a path relative to _WORKDIR_
  - All new files and directories are created with a UID and GID of 0 (root), unless the optional `--chown` flag specifies a given username, groupname, or UID/GID combination
  - **ADD** has two forms:

    1. `ADD [--chown=<user>:<group>] <src>... <dest>`
    2. `ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]`

      - _This form is required for paths containing whitespace_

- **COPY**

  - _PURPOSE_: Copies files from _src_ and adds them to the filesystem's _**CONTAINER**_ @ _dest_
  - _dest_ is an absolute path, or a path relative to _WORKDIR_
  - All new files and directories are created with a UID and GID of 0 (root), unless the optional `--chown` flag specifies a given username, groupname, or UID/GID combination
  - **COPY** has two forms:

    1. `COPY [--chown=<user>:<group>] <src>... <dest>`
    2. `COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]`

      - _This form is required for paths containing whitespace_

- **CMD**

  - _PURPOSE_: Provides defaults for an executing container. These defaults can include an executable, or they can omit the executable, in which case you must specify an **ENTRYPOINT** instruction as well.
  - CMD has three forms:

    1. `CMD ["executable", "args1", "args2"]` (_exec_ form, preferred)
    2. `CMD ["param1","param2"]` (as _default parameters to ENTRYPOINT_)
    3. `command param1 param2`

- **ENTRYPOINT**

  - _PURPOSE_: Allows you to configure a container that will run as an executable.
  - Will overwrite all elements specified with **CMD**. Can be overwritten by passing the `-entrypoint` flag when executing `docker run ...`.
  - Two forms:

    1. `ENTRYPOINT ["executable", "param1", "param2"]` (_exec_ form, preferred)
    2. `ENTRYPOINT command param1 param2` (_shell_ form)

- **ENV**

  - _PURPOSE_: Sets an environment variable for all future builds, which will affect all users.
  - **ENV** has two forms:

    1. `ENV myDog Rex The Dog`
    2. `ENV myName="John Doe" myDog=Rex\ The\ Dog" myCat=fluffy`

      - If you're passing multiple environment variables, you need to use **=**

- **EXPOSE**

  - _PURPOSE_: Tells the container to listen on the specified network port(s) at runtime.
  - For security reasons, you need to explicitly state with an **EXPOSE** statement which ports and protocols you want to redirect.

    - **EXPOSE** acts as a form of _documentation_ for what ports are intended to be redirected. Port redirection is actually applied by the `-p` or `-P` flag:

      - `-p`: Will redirect specific ports

        - Example: `-p 443:443/tcp -p 443/443/udp`
        - Passing this in your `docker run ...` statement will override **EXPOSE**

      - `-P`: Will redirect all posts explicity stated within your Dockerfile using **EXPOSE**

        - Downside of using `-P` is it will use ephemeral port ranges for redirection. This means UDP and TCP ports will most likely be different.

  - **EXPOSE** has one form:

    - `EXPOSE <port> [<port>/<protocol>...]`

- **FROM**

  - PUROSE: References the base image the Dockerfile will reference to instantiate your container
  - FROM has three forms:

    1. `FROM <image> [AS <name>]`
    2. `FROM <image>[:<tag>] [AS <name>]`
    3. `FROM <image>[@<digest>] [AS <name>]`

- **LABEL**

  - _PURPOSE_: Adds ≥1 metadata to an image via dictionary data set.
  - Labels included in the **FROM** image are inherited. If multiple labels exist with different values, the most recent one will apply.

- **RUN**

  - _PURPOSE_: Will execute commands in a new layer on top of the current image commit the results.
  - There can only be ONE **CMD** instruction in a Dockerfile. If > 1 are listed, only the last **CMD** will take effect.
  - **RUN** has two forms:

    1. `RUN ["executable", "arg1", "arg2"]` (_exec_ , preferred)

      ```
      `RUN ["c:\\windows\\system32\\tasklist.exe"]`
      ```

    2. `RUN <command>` (_shell_ form)

      - Runs in shell. Linux: `/bin/sh -c` | Windows: `cmd /S /C`

- **USER**

  - _PURPOSE_: Inherits the context of the user (and group) in the image instantiation for any **RUN**, **CMD**, or **ENTRYPOINT** commands that follow.
  - Commands not specified to run as a particular user will be run as Root during image instantiation.
  - **USER** has one Form: `USER <user>[:<group>]` or `USER <UID>[:<GID>]`

- **VOLUME**

  - _PURPOSE_: Creates a mount point with the specified name and marks it as holding externally mounted volumes from other hosts or containers.
  - Notes:

    - If mounting on Windows, destination must be non-existing or empty directory or a drive other than **C:**
    - Due to portable nature of containers, you must specify the mountpoint when you create or run the container using `-v` or `--mount`.

      - More information on [Using Volumes](https://docs.docker.com/storage/volumes/)

  - **VOLUME** has two forms:

    1. `VOLUME /var/log /var/db/`
    2. `VOLUME ["var/log", "/var/db"]`

- **WORKDIR**

  - _PURPOSE_: Sets the working directory for any **RUN**, **CMD**, **ENTRYPOINT**, **COPY**, and **ADD** instructions.
  - If **WORKDIR** doesn't exist, it will be created.
  - Multiple **WORKDIR** statements can be used in a Dockerfile (_read from top down_)

### General Caveats

- _Exec_ form will be parsed as JSON, so you must use double quotation marks **""**.
- It is necessary to escape backslashes in **exec** mode.

## Docker Images

- Download an Image

  - `docker pull <image_name>[:tag]`

- Remove an Image

  - `docker rmi <image_name>`
  - `docker rmi <image_id>`

    - Will remove all images with the same image_id

- Commit an Image

  - `docker commit container_name centos:custom`

- Save an Image

  - `docker save --output centos.custom.tar centos:latest`

- Load an Image

  - `docker load --input centos.custom.tar.gz`

- View Image History

  ```
  -   `docker history centos:custom`
  ```

  ## Naming Containers

  ```
  -   `docker run -d --name container_name ubuntu:latest`
  -   `docker rename sneaky_cauldron Mischief_Managed`
  ```

  ## Building Image from Dockefile

  ```
  -   `docker image built -t "repository:container_name" .`
      -   `-t` Adds a tag to your image. `.` tells Docker to use the Dockerfile found in the current directory.
  ```

  ## Remove Docker Container

  ```
  -   `docker rm container_name`
  ```

  ## Remove All Docker Containers

  ```
  -   `docker rm $(docker rm -a)`
  ```

### Commit Changes to a Docker Image

```
-   `docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]`
    -   OPTIONS:
        -   \--author, -a
        -   \--change, -c
        -   \--message, -m
        -   \--pause, -p
```

## Docker Networking

### Create a Bridge Adapter

- `docker network create -d bridge --subnet 10.1.0.0/16 --gateway 10.1.0.1 --ip-range=10.1.4.0/24 sample_bridge_1`

  - `--ip-range`: Containers with this network will be leased an unassigned IP from this ranges

### Applying the Bridge with Specific IP

- `docker run -it --name NAME --net sample_bridge_1 --ip 10.1.4.10 centos:latest /bin/bash`

### Delete a Bridge Adapter

- `docker network rm sample_bridge_1`

### Redirecting Port

- `docker run -d -p 80 nginx:latest`

  - Will map port 80/TCP to an ephemeral port on all of host's interfaces

- `docker run -d -p 8080:80 nginx:latest`

  - Will map port 80/TCP to 8080/TCP on all of host's interfaces

- `docker run -d -p 127.0.0.1:8080:80 nginx:latest`

  - Will map port 80/TCP to 8080/TCP only on host's localhost interface

## Docker Monitoring

### Event Filtering

- `docker events --filter | -f event=attach`
- `docker events -f event=start -f event=stop --since 10m`

  - More Event Filters can be found on [Docker :: Events](https://docs.docker.com/engine/reference/commandline/events/#extended-description)
