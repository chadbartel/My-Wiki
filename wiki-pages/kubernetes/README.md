# Kubernetes

This is the root wiki page for Kubernetes.

## Table of Contents

* [Return to the main page...](../../README.md)
* [Summary](#summary)
* [Install `kubectl` binary with curl on Linux](#install-kubectl-binary-with-curl-on-Linux)
* [Install `kubectl` using native package management](#install-kubectl-using-native-package-management)
* [Setup Kubernetes on WSL2 with Docker Desktop](#setup-kubernetes-on-wsl2-with-docker-desktop)

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

### Setup Kubernetes on WSL2 with Docker Desktop

The following steps should recreate the exact local Kubernetes setup I have on my home desktop and laptop.

1. [Install WSL2 on Windows 10](https://docs.microsoft.com/en-us/windows/wsl/install-win10).

2. Install a Linux distribution through the Microsoft Store ([Ubuntu 20.04](https://www.microsoft.com/store/apps/9n6svws3rx71) is recommended).

3. Launch the installed Linux distribution and create a username and password.

4. Update and upgrade Linux.

    ```bash
        # Update the repositories and list of the packages available
        sudo apt update
        # Update the system based on the packages installed > the "-y" will approve the change automatically
        sudo apt upgrade -y
    ```

5. Update `sudoers` to not require a password.

    ```bash
        # Edit the sudoers with the visudo command
        sudo visudo

        # Change the %sudo group to be password-less
        %sudo   ALL=(ALL:ALL) NOPASSWD: ALL

        # Press CTRL+X to exit
        # Press Y to save
        # Press Enter to confirm
    ```

6. Set WSL2 as the default WSL version in a new terminal window.

    ```bash
        wsl --set-default-version 2
    ```

7. [Download and install Docker Desktop for Windows](https://www.docker.com/products/docker-desktop).

8. Enable WSL2 integration with the installed Linux distribution in the Docker dashboard and restart Docker Desktop.

9. Ensure `docker` is available through WSL2 with the following command:

    ```bash
        docker version
    ```

10. [Enable Kubernetes through Docker Desktop](https://docs.docker.com/desktop/kubernetes/#enable-kubernetes) and restart WSL2 (close current terminal and open a new one).

11. Ensure `kubectl` is available through WSL2 with the following command:

    ```bash
        kubectl version
    ```

12. In WSL2, run the following commands to install [KinD](https://kind.sigs.k8s.io/docs/user/quick-start/):

    ```bash
        # Download the latest version of KinD
        curl -Lo ./kind https://github.com/kubernetes-sigs/kind/releases/download/v0.9.0/kind-linux-amd64
        # Make the binary executable
        chmod +x ./kind
        # Move the binary to your executable path
        sudo mv ./kind /usr/local/bin/
    ```

13. Create the directory `$HOME/.kube`, `$HOME/.minikube`, and create a new environment `KUBECONFIG`:

    ```bash
        # Set the Kubernetes configuration environment variable
        export KUBECONFIG=$HOME/.kube/config
        # Change the owner of the .kube and .minikube directories
        sudo chown -R $USER $HOME/.kube $HOME/.minikube
    ```

14. Create a Kubernetes cluster with `kind` and then delete it (we just want to create a config file):

    ```bash
        # Create the cluster and give it a name (optional)
        kind create cluster --name wslkind
        # Check if the .kube has been created and populated with files
        ls $HOME/.kube
        # Check how many nodes it created
        kubectl get nodes
        # Check the services for the whole cluster
        kubectl get all --all-namespaces
        # Delete the existing cluster
        kind delete cluster --name wslkind
    ```

15. Install the following required packages to enable `systemd`:

    ```bash
        # Install the needed packages
        sudo apt install -yqq daemonize dbus-user-session fontconfig
    ```

16. Create the following files to enable `systemd`:

    * `start-systemd-namespace`:

        ```bash
            # Create the start-systemd-namespace script
            sudo vi /usr/sbin/start-systemd-namespace
            #!/bin/bash

            SYSTEMD_PID=$(ps -ef | grep '/lib/systemd/systemd --system-unit=basic.target$' | grep -v unshare | awk '{print $2}')
            if [ -z "$SYSTEMD_PID" ] || [ "$SYSTEMD_PID" != "1" ]; then
                export PRE_NAMESPACE_PATH="$PATH"
                (set -o posix; set) | \
                    grep -v "^BASH" | \
                    grep -v "^DIRSTACK=" | \
                    grep -v "^EUID=" | \
                    grep -v "^GROUPS=" | \
                    grep -v "^HOME=" | \
                    grep -v "^HOSTNAME=" | \
                    grep -v "^HOSTTYPE=" | \
                    grep -v "^IFS='.*"$'\n'"'" | \
                    grep -v "^LANG=" | \
                    grep -v "^LOGNAME=" | \
                    grep -v "^MACHTYPE=" | \
                    grep -v "^NAME=" | \
                    grep -v "^OPTERR=" | \
                    grep -v "^OPTIND=" | \
                    grep -v "^OSTYPE=" | \
                    grep -v "^PIPESTATUS=" | \
                    grep -v "^POSIXLY_CORRECT=" | \
                    grep -v "^PPID=" | \
                    grep -v "^PS1=" | \
                    grep -v "^PS4=" | \
                    grep -v "^SHELL=" | \
                    grep -v "^SHELLOPTS=" | \
                    grep -v "^SHLVL=" | \
                    grep -v "^SYSTEMD_PID=" | \
                    grep -v "^UID=" | \
                    grep -v "^USER=" | \
                    grep -v "^_=" | \
                    cat - > "$HOME/.systemd-env"
                echo "PATH='$PATH'" >> "$HOME/.systemd-env"
                exec sudo /usr/sbin/enter-systemd-namespace "$BASH_EXECUTION_STRING"
            fi
            if [ -n "$PRE_NAMESPACE_PATH" ]; then
                export PATH="$PRE_NAMESPACE_PATH"
            fi
        ```
    
    * `enter-systemd-namespace`:

        ```bash
            # Create the enter-systemd-namespace
            sudo vi /usr/sbin/enter-systemd-namespace
            #!/bin/bash

            if [ "$UID" != 0 ]; then
                echo "You need to run $0 through sudo"
                exit 1
            fi

            SYSTEMD_PID="$(ps -ef | grep '/lib/systemd/systemd --system-unit=basic.target$' | grep -v unshare | awk '{print $2}')"
            if [ -z "$SYSTEMD_PID" ]; then
                /usr/sbin/daemonize /usr/bin/unshare --fork --pid --mount-proc /lib/systemd/systemd --system-unit=basic.target
                while [ -z "$SYSTEMD_PID" ]; do
                    SYSTEMD_PID="$(ps -ef | grep '/lib/systemd/systemd --system-unit=basic.target$' | grep -v unshare | awk '{print $2}')"
                done
            fi

            if [ -n "$SYSTEMD_PID" ] && [ "$SYSTEMD_PID" != "1" ]; then
                if [ -n "$1" ] && [ "$1" != "bash --login" ] && [ "$1" != "/bin/bash --login" ]; then
                    exec /usr/bin/nsenter -t "$SYSTEMD_PID" -a \
                        /usr/bin/sudo -H -u "$SUDO_USER" \
                        /bin/bash -c 'set -a; source "$HOME/.systemd-env"; set +a; exec bash -c '"$(printf "%q" "$@")"
                else
                    exec /usr/bin/nsenter -t "$SYSTEMD_PID" -a \
                        /bin/login -p -f "$SUDO_USER" \
                        $(/bin/cat "$HOME/.systemd-env" | grep -v "^PATH=")
                fi
                echo "Existential crisis"
            fi
        ```

        * Make this file an executable:

            ```bash
                # Edit the permissions of the enter-systemd-namespace script
                sudo chmod +x /usr/sbin/enter-systemd-namespace
            ```

17. Edit the `bash.bashrc` file to automatically enable systemd on login:

    ```bash
        # Edit the bash.bashrc file
        sudo sed -i 2a"# Start or enter a PID namespace in WSL2\nsource /usr/sbin/start-systemd-namespace\n" /etc/bash.bashrc
    ```

18. Install [minikube](https://minikube.sigs.k8s.io/docs/start/) on the host machine (Windows 10):

19. From a host machine terminal, create a Kubernetes cluster with `minikube` and then delete it (we just want to create a config file):

    ```bash
        minikube start
        minikube delete --all
    ```

20. From a WSL2 terminal, create the file `$HOME/minikube/minikube` and add the following lines to it:

    ```bash
        #!/bin/bash
        "/mnt/c/Program Files/Kubernetes/Minikube/minikube.exe" $@
    ```

    * Or the path wherever minikube was installed.

21. Make `$HOME/minikube/minikube` an executable:

    ```bash
        sudo chmod +x $HOME/minikube/minikube
    ```

22. Add the following environment variables:

    ```bash
        export PATH="$HOME/minikube:$PATH"
        export DOCKER_CERT_PATH=/mnt/c/Users/[YOUR-USER]/.minikube/certs
    ```

23. Add the following to `~/.bashrc` to automatically copy the host Kubernetes config file to WSL2:

    ```bash
        # Copy host KUBECONFIG to WSL
        cp /mnt/c/Users/Chaddle/.kube/config $HOME/.kube/config-host
        # Replace host paths in KUBECONFIG to WSL paths
        sed -i 's_\\_/_g' $HOME/.kube/config-host
        sed -i 's_[Cc]:_/mnt/c_g' $HOME/.kube/config-host
        
        # Append the new config file to the existing KUBECONFIG environment variable
        export KUBECONFIG=$KUBECONFIG:$HOME/.kube/config-host
    ```
