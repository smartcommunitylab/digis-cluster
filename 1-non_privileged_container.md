# Run Docker container as a non-root user
The Docker containers by default run with the root privilege and so does the application that runs inside the container.

## Steps

 1. Log in to the server with your username
 2. Retrieve your host username and group ID (**uid** and **gid**)
command:
 ```bash
standard@digis-dgx2:~$ id
```
 output:
 ```bash
uid=1000(standard) gid=1000(standard) groups=1000(standard),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),118(lxd),122(lpadmin),123(sambashare),999(docker)
```
 3.	Edit your Dockerfile to use non-root user
		(using **uid** and **gid** from the previous step, allows you to manage files written by the container with your host username).
```dockerfile
FROM your-base-image:latest
ARG USER=standard
ARG USER_ID=1000 # uid from the previus step
ARG USER_GROUP=standard
ARG USER_GROUP_ID=1000 # gid from the previus step
ARG USER_HOME=/home/${USER}
# create a user group and a user (this works only for debian based images)
RUN groupadd --gid $USER_GROUP_ID $USER \
    && useradd --uid $USER_ID --gid $USER_GROUP_ID -m $USER
# setup image istructions
RUN apt-get update && apt-get install -y curl
# set container user
USER $USER
# run script as non-root user
ENTRYPOINT ["python", "helloworld.py"]
```
