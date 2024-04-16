## Docker Installations

Add Docker's official GPG keys

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

> If you got notification that's indicate to overwrites the exiting docker gpg keys, select `yes`

Add the repository to Apt sources

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Install all required docker programs

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

To run docker without sudo privileges or root user, add your user to `docker` group

```bash
sudo usermod -aG docker $USER
sudo chmod 666 /var/run/docker.sock 
```

Try to run docker command without sudo privileges or root user

```bash
lnearher@lnear-labs:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
lnearher@lnear-labs:~$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
nginx         latest    c613f16b6642   2 months ago    187MB
hello-world   latest    d2c94e258dcb   11 months ago   13.3kB
lnearher@lnear-labs:~$ docker pull alpine:latest
latest: Pulling from library/alpine
4abcf2066143: Pull complete
Digest: sha256:c5b1261d6d3e43071626931fc004f70149baeba2c8ec672bd4f27761f8e1ad6b
Status: Downloaded newer image for alpine:latest
docker.io/library/alpine:latest
```

Reference:

- https://docs.docker.com/engine/install/ubuntu/
- https://docs.docker.com/engine/install/linux-postinstall/





