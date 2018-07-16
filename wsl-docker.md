# (Optional) Making Docker Work With WSL
This section is not strictly needed for using Docker on Windows. However, heavy users of WSL may find this convenient... after all the set up is done.

Note that if you have any symlinks in WSL, one of the following steps may render all of them inoperable and you will have to re-create those symlinks. This is due to an alteration of the WSL's directory structure.

The following steps assume that you're running Windows 10 18.03.

## W10 Home Users: Check your docker host IP address
1. Open the Docker Quickstart Terminal and run `docker-machine env`. Make a note of the value of the `DOCKER_HOST` variable.

## Installing the Docker Client in WSL
Run the following commands in order

#### Environment variables for the installation scripts ####
1. `export DOCKER_CHANNEL=edge`
2. `export DOCKER_COMPOSE_VERSION=1.21.0`

#### Adding Docker's repository and key and installing it ####
3. `sudo apt-get update`
4. `sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common`
5. `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
6. `sudo apt-key fingerprint 0EBFCD88`
7. `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) ${DOCKER_CHANNEL}"`
8. `sudo apt-get update`
9. `sudo apt-get install -y docker-ce`

#### Adding your user to the docker group so that you can access the Docker CLI without sudo ####
10. `sudo usermod -aG docker $USER`

#### Installing Docker Compose ####
11. ```sudo curl -L https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose &&
sudo chmod +x /usr/local/bin/docker-compose```
12. Close and reopen WSL


#### Getting your WSL Docker CLI to communicate with the Docker Daemon ####
13. `nano ~/.bashrc` and add the following 3 lines to the bottom of the file.
    - `export DOCKER_HOST=tcp://0.0.0.0:2376` 
        - *__W10 Home Users:__ Use your docker host ip address from the previous section instead of `0.0.0.0` here*
    - `export DOCKER_CERT_PATH="/c/Users/<YOUR WINDOWS USER NAME HERE>/.docker/machine/certs"`
    - `export DOCKER_TLS_VERIFY=1`

## The Part That Invalidates Symlinks in WSL - Making Volume Mounts Work

Run `sudo nano /etc/wsl.conf` and type the following.

```
[automount]
root = /
options = "metadata"
```

Hit `ctrl-o`, save the file, `ctrl-x` to exit.

Reboot Windows for the changes to fully take effect and avoid errors with the WSL within the session.

The above file sets your root directory of each hard disk to `/c` or `/d` instead of the WSL defaults of `/mnt/c` or `/mnt/d`. Docker expects the former. Hence, this breaks symlinks, and if you have any, you will need to re-link them.
