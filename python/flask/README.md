Nublar - Python Flask example

## Overview

This is a one line Flask application to demonstrate deployment to Digital Ocean with Nublar.

## project requirements

-   `.python-version` file

    Your repository needs this file to contain the version of python you are targeting.

-   Install the latest [Packer](https://www.packer.io/downloads.html)

-   Install the latest [Terraform](https://www.terraform.io/downloads.html)

## build the machine image

-   `nublar/nublar.json`.

    Set build parameters in this file

-   `do_ssh_username`

     this key designates the username to login with.  default=root.

-   Run packer to create a new snapshot:

    ```sh
    nublar$ export DIGITALOCEAN_API_TOKEN=...
    nublar$ packer build -var-file=variables/nublar.json imaging/packer/digitalocean/packer.json
    ```

-   Get the new snapshot ID from the output (or your Digital Ocean web panel):

    ```
    ==> Builds finished. The artifacts of successful builds are:
    --> digitalocean: A snapshot was created: 'nublar-15...' (ID: 29...) in regions ''
    ```

## create the droplet

-   Initialize Terraform the first time:

    ```sh
    infrastructure_management/terraform/digitalocean$ terraform init
    ```

-   Update the `do_image` key in `vars.tf` with your desired snapshot id.

-   Update the `do_ssh_keys` key in `vars.tf` with at least one of your ssh keys from your DO account.

-   Try to plan with terraform

    ```sh
    $ export TF_VAR_DIGITALOCEAN_TOKEN=...
    infrastructure_management/terraform/digitalocean$ terraform plan -var-file ../../../variables/nublar.json -out tfplan
    ```

-   If the plan looks ok, create the droplet

    ```sh
    infrastructure_management/terraform/digitalocean$ terraform apply tfplan
    ```

-   Cleanup the old plan, do not use it again

    ```sh
    infrastructure_management/terraform/digitalocean$ rm tfplan
    ```

-   The default ssh name is `root`.  Check packer/digitalocean.json to be sure.

## destroy the droplet

-   This will remove your droplet:

    ```sh
    nublar/terraform$ terraform destroy
    ```

## example project development
-   Install [pyenv](https://github.com/pyenv/pyenv)

    Make sure you get your shell to init properly!

-   Setup [pipenv](https://github.com/kennethreitz/pipenv) using the provided convenience script

    This is going to install a recent version of Python into your pyenv.

    `./pipenv_setup.sh`