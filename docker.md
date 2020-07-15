Taken & summarized from: https://www.dataraccoon.com/knowledge/docker

## Checking Docker Installation
```
docker --help 
which docker
```

## Downloading a Docker Image
```
docker pull {imagename:tag}
```

## Listing Downloaded Images
```
docker images
```

## Containers
A container is an instance of an image. Image is the recipe, container is one recreation/dish of the recipe.
```
docker run -it -p 8888:8888 continuumio/anaconda3
```
Instantiating a container requires the image name to be specified. Port is required for your web browser to access the API/jupyter notebook/etc. To check the running containers (This must be run outside of the docker terminal/session): 
```
docker ps (To show only the currently running containers)

docker ps -a (To show historical containers as well)
```
`exit` command can be used to quit the currently running docker terminal instance.

From `docker ps` command, `CONTAINER ID` can be obtained, and restarting a docker container can be done via: 
```
docker start {CONTAINER ID}
```
To access the bash terminal:
```
docker exec -it {CONTAINER ID} bash
```

## Scripting the Dockerfile
The above commands (eg. starting a jupyter notebook), and also setting an environment variable and mounting a volume can be done via a `Dockerfile`.

Example of a `Dockerfile`:
```
FROM continuumio/miniconda3:4.8.2
EXPOSE 8888
COPY requirements.txt $HOME
RUN pip install -r requirements.txt
WORKDIR $HOME/workenv
CMD jupyter notebook --ip 0.0.0.0 --port 8888 --no-browser --allow-root
```
`FROM` statement is in the format of `{IMAGENAME}:{IMAGETAG}` specifies from which image and which version of the image is the container to be created from.

`:latest` can be specified in the `:{IMAGETAG}` part to always take the latest release of the image.
```
Command	Purpose
FROM - base image
COPY - copy files to docker
EXPOSE - port to expose
WORKDIR -	the base directory of docker
RUN -	run commands inside docker, eg pip install
ENTRYPOINT - configure how container will run
CMD -	last command to run
```

### Using a Dockerfile 
After the `Dockerfile` has been built, we can built a custom image based on the base image and we can specify additional build steps based on the specified base image into a new image.
```
docker build -t {image_name} {Dockerfile directory}
docker run -it -p 8888:8888 {image_name}
```

## Mounting A Volume 
A volume is used to share files in realtime between the local machine/hard drive and the docker container environment. Example of use is to store the training data, and/or have a jupyter notebook written in a docker environment, yet saved in a place accessible from local machine.

```
docker run --rm -it -p 8888:8888 \
-v $(pwd)/workenv:/workenv \
 docker_notebook
```

- The --rm flag tells your system to remove your container once you exit the session. This is to make sure you do not store data in your container to prevent 'mishaps'.

Multiple `-v` arguments can be used to mount multiple directories.
 
## Managing secrets and environment variables
Similar to `.gitignore`, docker has `.dockerignore`. Directory containing confidential files and informations can be specified to avoid copying it over to the image.

Sample directory:
```
.
├── .dockerignore
├── Dockerfile
├── requirements.txt
├── secrets
│   └── env_vars
└── workenv
    ├── create.ipynb
    ├── anyfiles.csv
    └── hello_world.py
```

Then, we can specify the `.dockerignore` to be: 
```
**/secrets/*
```
to avoid the contents of the `secrets` folder to be carried over into the image.

Instead, the contents can be passed as environment variables using either `--env-file` and/or `-e` option like below: 
```
docker run --rm -it -p 8888:8888 \
--env-file ./secrets/env_vars \
-e db_table=hellothere \
-v $(pwd)/workenv:/workenv \
--entrypoint bash \
docker_notebook
```

We can also mount the secrets folder for us to access the files inside (eg.JSON config file) `ONLY DURING DEVELOPMENT TIME`.

## Docker Python Runs
`sys.args` can be passed (1 index) by arguments following the python file: 
```
docker run -it dockerpy eg2.py hello there
```
Specifying things after the `dockerpy` overwrites the `CMD` inside the `Dockerfile` (https://goinbigdata.com/docker-run-vs-cmd-vs-entrypoint/).

`argparse` can be used similarly like below: 

```
docker run -it dockerpy eg3.py --greet hello --name myname --animal beef
docker run -it dockerpy eg3.py --greet hello --name myname --animal beef --animal chicken --amount 10
```
