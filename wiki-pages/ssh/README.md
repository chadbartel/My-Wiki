# SSH

This is the root wiki page for SSH.

## Table of Contents

* [Return to the main page...](../../README.md)
* [Summary](#summary)
* [Auto-Start SSH Agent in Bash](#auto-start-ssh-agent-in-bash)
* [Add SSH to GitHub](#add-ssh-to-github)

### Summary

From [Wikipedia](https://en.wikipedia.org/wiki/Secure_Shell_Protocol):

> **SSH** (secure shell protocol) is a cryptographic network protocol for operating network services securely over an unsecured network. Typical applications include remote command-line, login, and remote command execution, but any network service can be secured with SSH.

### Auto-Start SSH Agent in Bash

This should only prompt for a password the first time you login after each reboot. It will keep reusing the same `ssh-agent` as long as it stays running. This solution was copied from [this Stack Exchange question](https://unix.stackexchange.com/questions/90853/how-can-i-run-ssh-add-automatically-without-a-password-prompt).

 1. Add the following to your `~/.bashrc` file:

    ```bash
        if [ ! -S ~/.ssh/ssh_auth_sock]; then
        eval `ssh-agent`
        ln -sf "$SSH_AUTH_SOCK" ~/.ssh/ssh_auth_sock
        fi
        export SSH_AUTH_SOCK=~/.ssh/ssh_auth_sock
        ssh-add -l > /dev/null || ssh-add
    ```

 2. Logout and log back in for the change to take effect.

 3. *Optional*: If you would like to automatically add an SSH key to the agent, add the following line anywhere below the script you added in step #1:

    ```bash
        ssh-add ~/.ssh/your_private_ssh_key
    ```

    * This should only be necessary if you have a private key file that isn't named something like `id_rsa` or `id_ed25519`.

Alternatively, you can install `keychain` and simply add the keys to your keychain. This solution will ensure that the `ssh-agent` will use the key you specify only when you call the SSH service, not when you open bash or login. This was copied from [this Stack Overflow question](https://stackoverflow.com/questions/52423626/remember-git-passphrase-in-wsl).

 1. Install `keychain`:

    ```bash
        sudo apt-get update
        sudo apt-get install keychain
    ```

 2. Find your hostname in the terminal:

    ```bash
        hostname
    ```

 3. Add the following to either `~/.bashrc` or `~/.zshrc`:

    ```bash
        /usr/bin/keychain --nogui ~/.ssh/your_private_key
        source $HOME/.keychain/YOUR-HOSTNAME-HERE-sh
    ```

    * Yes, suffix the hostname with `-sh`.

Further information on how to use `keychain` can be found [here](https://www.cyberciti.biz/faq/ssh-passwordless-login-with-keychain-for-scripts/).

### Add SSH to GitHub

In order to control your repositories on GitHub using SSH, follow the steps below, or review the official documentation [here](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh):

1. Open Git Bash and check to see if you have an existing key:

    ```bash
        ls -al ~/.ssh
    ```

    * The default filenames for these keys are one of the following:
        * *id_rsa.pub*
        * *id_ecdsa.pub*
        * *id_ed25519.pub*

2. If you don't have a key or wish to create a new one anyways, create a new key associated with your GitHub account email address:

    ```bash
        ssh-keygen -t ed25519 -C "your_email@example.com"
    ```

3. Press "Enter" to accept the default file name and location.

4. When prompted, enter a password for your SSH key.

5. Start the `ssh-agent` in the background:

    ```bash
        eval "$(ssh-agent -s)"
    ```

6. Add your SSH *private* key to the `ssh-agent`:

    ```bash
        ssh-add ~/.ssh/id_ed25519
    ```

7. Copy your SSH *public* key to your clipboard.

8. Go to GitHub.com, login, and navigate to **Settings**.

9. On the left-hand sidebar, click **SSH and GPG keys**.

10. Click **New SSH key**.

11. Provide a descriptive title in the "Title" field.

12. Paste the key you copied to your clipboard into the "Key" field.

13. Click "Add SSH key**.

14. If prompted, confirm your GitHub password.

15. Test your SSH key with the following command:

    ```bash
        ssh -T git@github.com
    ```
