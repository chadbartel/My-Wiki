# Docker

This is the root wiki page for Docker.

## Table of Contents

* [Return to the main page...](../../README.md)
* [Summary](#summary)
* [Installing Docker Desktop](#installing-docker-desktop)
* [Installing Docker Engine on Ubuntu](#installing-docker-engine-on-ubuntu)
* [Running Docker without sudo](#running-docker-without-sudo)
* [WSL cannot connect to the Docker daemon error](#wsl-cannot-connect-to-the-docker-daemon-error)

### Summary

From [Wikipedia](https://en.wikipedia.org/wiki/Docker_(software)):

> **Docker** is a set of platform as a service products that use OS-level virtualization to deliver software in packages called containers. Containers are isolated from one another and bundle their own software, libraries and configuration files; they can communicate with each other through well-defined channels. 

### Installing Docker Desktop

You can install Docker Desktop by following the link [here](https://www.docker.com/products/docker-desktop) to download and run the installer.

### Installing Docker Engine on Ubuntu

Follow the steps below to install Docker Engine on Ubuntu.

1. Ensure you uninstall any older versions of Docker Engine:

    ```bash
        sudo apt-get remove docker docker-engine docker.io containerd runc
    ```

2. Update your system and install the dependencies:

    ```bash
        sudo apt-get update

        sudo apt-get install \
            apt-transport-https \
            ca-certificates \
            curl \
            gnupg \
            lsb-release
    ```

3. Add Docker's GPG key:

    ```bash
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    ```

4. Set up the repository for the **stable** version of Docker Engine:

    ```bash
        echo \
        "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```

5. Update the system and install Docker Engine:

    ```bash
        sudo apt-get update
        sudo apt-get install docker-ce docker-ce-cli containerd.io
    ```

6. Verify that Docker Engine installed correctly:

    ```bash
        sudo docker run hello-world
    ```

### Running Docker without sudo

If you don't want to constantly include `sudo` in your calls to `docker`, then follow the steps below.

1. Create the `docker` group:

    ```bash
        sudo groupadd docker
    ```

2. Add your user to the `docker` group:

    ```bash
        sudo usermod -aG docker $USER
    ```

3. Log out and then log back in.

4. Activate the changes to groups:

    ```bash
        newgrp docker
    ```

5. Verify that you can call `docker` without `sudo`:

    ```bash
        docker run hello-world
    ```

### WSL cannot connect to the Docker daemon error

Have you ever been running in WSL2 and encountered this error?

```bash
    docker: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?.
```

You're not alone! This is apparently a common issue with WSL2 and Docker. You'll look online for an answer and a lot of people will tell you to use `systemctl`. However, this *also* won't work because you're using WSL2. So, what do we do?

**Gigantic** thanks to [JERRY](https://stackoverflow.com/users/3312462/jerry) on [this StackOverflow question](https://stackoverflow.com/questions/20680050/how-do-i-install-chkconfig-on-ubuntu) which I was lead to from [this article on LinuxHandbook.com](https://linuxhandbook.com/system-has-not-been-booted-with-systemd/). The table below will show you the ***revised*** equivalents from the table found in the [LinuxHandbook.com](LinuxHandbook.com) article.

| **Systemd command** | **Sysvinit command** |
|---|---|
| `systemctl start service_name` | `service service_name start` |
| `systemctl stop service_name` | `service service_name stop` |
| `systemctl restart service_name` | `service service_name restart` |
| `systemctl status service_name` | `service service_name status` |
| `systemctl enable service_name` | `update-rc.d service_name enable` |
| `systemctl disable service_name` | `update-rc.d service_name disable` |
