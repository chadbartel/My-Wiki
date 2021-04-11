# WSL

This is the root wiki page for Terraform.

## Table of Contents

* [Return to the main page...](../../README.md)
* [Summary](#summary)
* [Install Terraform on Ubuntu](#install-terraform-on-ubuntu)

### Summary

From [Wikipedia](https://en.wikipedia.org/wiki/Terraform_(software)):

> **Terraform** is an open-source infrastructure as code software tool created by HashiCorp. Users define and provide data center infrastructure using a declarative configuration language known as HashiCorp Configuration Language (HCL), or optionally JSON. Terraform manages external resources (such as public cloud infrastructure, private cloud infrastructure, network appliances, software as a service, and platform as a service) with "providers". HashiCorp maintains an extensive list of official providers, and can also integrate with community-developed providers. Users can interact with Terraform providers by declaring resources or by calling data sources. Rather than using imperative commands to provision resources, Terraform uses declarative configuration to describe the desired final state. Once a user invokes Terraform on a given resource, Terraform will perform CRUD actions on the user's behalf to accomplish the desired state. The infrastructure as code can be written as modules, promoting reusability and maintainability.

### Install Terraform on Ubuntu

Follow the steps outlined below to install Terraform (from the documentation [here](https://learn.hashicorp.com/tutorials/terraform/install-cli)):

1. Add the HashiCorp GPG key:

    ```bash
        curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
    ```

2. Add the official HashiCorp Linux repository:

    ```bash
        sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
    ```

3. Update the system and install Terraform:

    ```bash
        sudo apt-get update && sudo apt-get install terraform
    ```

4. Verify the installation:

    ```bash
        terraform -help
    ```

* If you are using `bash` or `zsh` you can enable tab completion for Terraform commands by running the command below and restarting the shell:

    ```bash
        terraform -install-autocomplete
    ```
