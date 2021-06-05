# AWS

This is the root wiki page for AWS (Amazon Web Services).

## Table of Contents

* [Return to the main page...](../../README.md)
* [Summary](#summary)
* [Installing AWS CLI version 2 on Linux](#installing-aws-cli-version-2-on-linux)
* [Using the official AWS CLI version 2 Docker image](#using-the-official-aws-cli-version-2-docker-image)
* [Configuring AWS SSO](#configuring-aws-sso)
* [AWS SSO Credential Provider](#aws-sso-credential-provider)

### Summary

From [Wikipedia](https://en.wikipedia.org/wiki/Amazon_Web_Services):

> **Amazon Web Services** (AWS) is a subsidiary of Amazon providing on-demand cloud computing platforms and APIs to individuals, companies, and governments, on a metered pay-as-you-go basis. These cloud computing web services provide a variety of basic abstract technical infrastructure and distributed computing building blocks and tools.

### Installing AWS CLI version 2 on Linux

Follow the steps below in order to install AWS CLI version on Linux (official documentation [here](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-linux.html)):

1. Download the *latest* version of the AWS CLI with following command:

    ```bash
        curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    ```

    * You can download specific version of the AWS CLI by browsing the list of versions in the [changelog](https://github.com/aws/aws-cli/blob/v2/CHANGELOG.rst).

2. Unzip the contents of the zip file downloaded in the previous step:

    ```bash
        unzip awscliv2.zip
    ```

3. Install the AWS CLI:

    ```bash
        sudo ./aws/install
    ```

4. Confirm the installation was successful:

    ```bash
        aws --version
    ```

### Using the official AWS CLI version 2 Docker image

With Docker, you can create and run a container of the latest AWS CLI without having to manage the installation. This allows you to execute AWS CLI commands on the fly with a Docker container. You can read about this further in the official AWS CLI documentation [here](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-docker.html). In addition, you can review the details of this Docker image on their Docker Hub page [here](https://hub.docker.com/r/amazon/aws-cli).

1. Ensure you have Docker installed:

    ```bash
        docker --version
    ```

2. Run the official AWS CLI version 2 Docker image:

    ```bash
        docker run --rm -it amazon/aws-cli command
    ```

    * The command above is equivalent to the `aws` executable.
        * `--rm` = Removes the container when the command exits.
        * `-it` = Opens a "pseudo-TTY" with `stdin` to provide input to the AWS CLI version 2 while it's running in a container.
            * If you are running scripts, the `-it` option is not needed -- if you are experiencing errors with your scripts, omit `-it` from your command.

One of the advantages of using the offical Docker image is that you can specify which version you would like to use by specifying the tag. You can find a full list of available tags for this Docker image [here](https://hub.docker.com/r/amazon/aws-cli/tags?page=1&ordering=last_updated).

* The `latest` tag ensures you are using the most up-to-date version of the AWS CLI (excluding this tag uses `latest`, by default):

    ```bash
        docker run --rm -it amazon/aws-cli:latest command
    ```

* However, providing `<major.minor.patch>` as the tag will create and run a Docker container of the AWS CLI for the version specified in the tag:

    ```bash
        docker run --rm -it amazon/aws-cli:2.0.6 command
    ```

Another advantage is being able to control what files are and aren't shared with the container when it runs, for example, your AWS CLI credentials rather than explicitly typing in your access key and secret key.

* The following example will share the host file system `~/.aws` to the container root at `root/.aws` using `-v`:

    ```bash
        docker run --rm -it -v ~/.aws:/root/.aws amazon/aws-cli s3 ls
    ```

* You can pass environment variables from the host file system to the docker container with `-e`. The following example passes the `AWS_PROFILE` environment variable from the host file system to the Docker container:

    ```bash
        docker run --rm -it -v ~/.aws:/root/.aws -e AWS_PROFILE amazon/aws-cli s3 ls
    ```

* Sometimes, you need to download something from AWS using the CLI. The following example downloads a file from S3 to the current working directory, `pwd`:

    ```bash
        docker run --rm -it -v ~/.aws:/root/.aws -v $(pwd):/aws amazon/aws-cli s3 cp s3://aws-cli-docker-demo/hello .
    ```

Finally, you can even *alias* the command as `aws` in your terminal to avoid having to type the same thing over and over:

```bash
    alias aws='docker run --rm -it amazon/aws-cli'
```

Now, using the alias, it's practically indistinguishable from having it installed on your system!

### Configuring AWS SSO

Once you have AWS CLI version 2 installed on your system you'll be able to take advantage of using SSO with the CLI. The steps below walk you through the process of configuring an AWS profile with SSO enabled.

You can configure an AWS profile with SSO enabled either automatically or manually.

#### Automatic Configuration

1. Run the following command.

    ```bash
        aws configure sso
    ```

    * When prompted, provide your AWS SSO start URL and the host region of the AWS SSO directory.

2. The AWS CLI will open your default browser and prompt you to log in.

3. After you log in, you will be prompted to select an account based on your start URL and region.

4. Once you select an account, you will be prompted to select a role.

5. You will need to provide the default region, output format, and profile name for the selected account and role.

6. You can verify everything was configured correctly by executing each of the following commands:

    ```bash
        aws sso login --profile your-sso-profile-name

        aws s3 ls --profile your-sso-profile-name
    ```

    * Substitute out `your-sso-profile-name` with the name of the SSO profile you made in step 5.

    Alternatively, execute `cat ~/.aws/config` and verify your new profile is in the terminal output.

Once you set up an SSO profile, you can use all AWS CLI commands as you usally would in a terminal as long as you include `--profile your-sso-profile-name`.

### AWS SSO Credential Provider

Sometimes, logging into AWS SSO using `aws sso login --profile my-sso-profile` isn't sufficient for certain applications. For example, you may need to provide an access key, secret access key, and session token. The [**aws-sso-credential-provider**](https://github.com/kcerdena/aws_sso) solves this problem by fetching your AWS SSO profile credentials using [STS](https://docs.aws.amazon.com/STS/latest/APIReference/welcome.html).

Follow the steps below to install **aws-sso-credential-provider**.

1. Install AWS CLI version 2.

2. Configure at least *one* named SSO profile.

3. Use `pip` to install **aws-sso-credential-provider**:

    ```bash
        pip install aws-sso-credential-provider
    ```

4. Run the credential provider using Python:

    ```bash
        python3 -m aws_sso -p SSO_PROFILE
    ```

### Get SSO Profile Credentials Using STS

Thanks to the response from Rolando Cintron on [this](https://stackoverflow.com/questions/55128348/execute-terraform-apply-with-aws-assume-role) Stack Overflow question, here is a "bulletproof" way to get credentials from an AWS profile:

```bash
    aws_credentials=$(aws sts assume-role --role-arn arn:aws:iam::1234567890:role/nameOfMyrole --role-session-name "RoleSession1")

    export AWS_ACCESS_KEY_ID=$(echo $aws_credentials|jq '.Credentials.AccessKeyId'|tr -d '"')
    export AWS_SECRET_ACCESS_KEY=$(echo $aws_credentials|jq '.Credentials.SecretAccessKey'|tr -d '"')
    export AWS_SESSION_TOKEN=$(echo $aws_credentials|jq '.Credentials.SessionToken'|tr -d '"')
```

As a practical example, you could use this solution to test a Docker container that will be assuming an role in AWS but can only test locally:

```bash
    docker run \
        -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID \
        -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY \
        -e AWS_SESSION_TOKEN=$AWS_SESSION_TOKEN \
        myimage
```
