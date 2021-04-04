# SSH

This is the root wiki page for SSH.

## Table of Contents

 1. [Auto-Start SSH Agent in Bash](#auto-start-ssh-agent-in-bash)

### Auto-Start SSH Agent in Bash

This should only prompt for a password the first time you login after each reboot. It will keep reusing the same `ssh-agent` as long as it stays running. This solution was copied from [this StackExchange question](https://unix.stackexchange.com/questions/90853/how-can-i-run-ssh-add-automatically-without-a-password-prompt).

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
