# Kubernetes

This is the root wiki page for Kubernetes.

## Table of Contents

* [Return to the main page...](../../README.md)
* [Summary](#summary)
* [Install `kubectl` binary with curl on Linux](#install-kubectl-binary-with-curl-on-Linux)
* [Install `kubectl` using native package management](#install-kubectl-using-native-package-management)

### Summary

### Install `kubectl` binary with curl on Linux

1. Download the latest release.

    ```bash
        curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    ```

    * Alternatively, a specific version can be downloaded.

        ```bash
            curl -LO https://dl.k8s.io/release/v1.21.0/bin/linux/amd64/kubectl
        ```

2. Validate the binary.

    1. Download the `kubectl` checksum file.

        ```bash
            curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
        ```

    2. Validate the `kubectl` binary against the checksum file.

        ```bash
            echo "$(<kubectl.sha256) kubectl" | sha256sum --check
        ```

        * If valid, the output will read: `kubectl: OK`; otherwise, a nonzero status will print to the terminal.

3. Install `kubectl`.

    ```bash
        sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    ```

4. Confirm the installed version of `kubectl`.

    ```bash
        kubectl version --client
    ```

### Install `kubectl` using native package management

1. Update the `apt` package index and install packages needed to use the Kubernetes `apt` repository.

    ```bash
        sudo apt-get update
        sudo apt-get install -y apt-transport-https ca-certificates curl
    ```

2. Download the GOogle Cloud public signing key.

    ```bash
        sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
    ```

3. Add the Kubernetes `apt` repository.

    ```bash
        echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
    ```

4. Update the `apt` package index with the new repository and install `kubectl`.

    ```bash
        sudo apt-get update
        sudo apt-get install -y kubectl
    ```
