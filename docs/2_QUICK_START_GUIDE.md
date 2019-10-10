# 2. Quick Start Guide

## 2.1 Overview

*Cobalt* is a tool for developers who are interested in reusing or contributing new cloud infrastructure as code patterns in template form. An actualized infrastructure as code pattern in Cobalt is called a *Cobalt Infrastructure Template* or *CIT* (/kɪt/). Cobalt Infrastructure Templates primarily rely on [*Terraform*](https://learn.hashicorp.com/terraform)'s HCL language in order to target a wide array of cloud providers.

You can get pretty creative and build your own custom *CIT*s in order to use and/or contribute to Cobalt but we strongly recommend that you first complete this quick start guide. This guide is centered around our existing [*Azure Hello World CIT*](../infra/templates/az-hello-world/README.md "AZ Hello World - Cobalt Infrastructure Template") and should serve as your first Azure infrastructure deployment. In summary, completing this guide should be your first major step in familiarizing yourself with Cobalt and the *CIT* developer workflow. Happy templating! 😄

> For a more general overview of Cobalt, please visit our main page: [READ ME](https://github.com/microsoft/cobalt/blob/master/README.md "Main Cobalt Read Me")

## 2.2 Goals and Objectives

🔲 Prepare local environment for *Cobalt Infrastructure Template* deployments.

🔲 Deploy the [*Azure Hello World CIT*](../infra/templates/az-hello-world/README.md "AZ Hello World - Cobalt Infrastructure Template").

🔲 Walk away with a introductory understanding of the *CIT* developer workflow.

🔲 Feel confident in moving forward to our next recommended section: *[Cobalt Templating from Scratch](https://github.com/microsoft/cobalt/blob/master/docs/3_NEW_TEMPLATE.md).*

## 2.3 Prerequisites

> NOTE: Previous "infrastructure as code" experience is not a prerequisite for completing the quick start guide.

| Azure Prereqs | Description | I | Install Prereqs | Description |
|----------|--------------|-|----------|--------------|
| `Azure Subscription` | [Azure Portal](https://portal.azure.com/) - This template needs to deploy infrastructure within an Azure subscription.|I|`Terminal with bash shell`|[WSL](https://code.visualstudio.com/docs/remote/wsl) or [Git Bash](https://git-scm.com/downloads) - The shell environment needed to follow along with the provided instructions.|
|`Azure Service Principal`|[Azure Service Principal](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal) - This template needs permissions to deploy infrastructure within an Azure subscription.|I|`Git Source Control`|[Install git](https://www.atlassian.com/git/tutorials/install-git)|
|`Azure Storage Account`|[Azure Storage Account](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview) - An account for tracking terraform remote backend state. You can use our backend state setup [template](../infra/templates/backend-state-setup/README.md) to provision the storage resources.|I|`Terraform`|[Terraform](https://www.terraform.io/downloads.html) - Download the appropriate environment for setting up Terraform|
|`Azure CLI`|[Get started with Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)- An account for tracking terraform remote backend state. You can use our backend state setup [template](../infra/templates/backend-state-setup/README.md) to provision the storage resources.|I|`GitHub Account`|[Github](https://github.com/login) - An account for forking the Cobalt repo|

---

## 2.4 Deploy Cobalt's [_Azure Hello World CIT_](../infra/templates/az-hello-world/README.md)

Below are the steps for deploying the [_Azure Hello World CIT_](../infra/templates/az-hello-world/README.md). Ensure that this deployment outputs the following Azure cloud resources before you can call this quick start guide finished:

| Preview | Quick Start Azure Cloud Resources |
|----------|--------------|
|![cobalt-'NickeManarin/Screen2Gif'](./screentogif-40sec-test.gif)| <image src="https://user-images.githubusercontent.com/10041279/66533295-0fc64b80-ead8-11e9-96c0-ea5441cde854.png" width="1400"/> |

<!--- These gifs will have screenshots from forking, terraform plan and apply, visit azure portal and visit app service url --->

### **Step 1:** Fork Cobalt Repo

Initializing a repository that you own and control is recommended. Once forked, move onto the next step.

* From any page within this repository, find the forked menu and fork the repo into your own repository.

    ![image](https://user-images.githubusercontent.com/10041279/66366857-6e17f080-e957-11e9-8b32-266b0d4a98fc.png)

### **Step 2:** Clone Repo to Local Environment

You will be deploying Azure infrastructure for your local environment so you will need to have a copy of the Cobalt project locally.

* From any terminal with git, use the following git command to clone your forked repo into your local environment. You can find your git repo url at the landing page of your forked repo.

    ```bash
    git clone <insert-forked-git-repo-url> # ex. git clone https://github.com/<YourGitAccout>/cobalt.git
    ```

### **Step 3:** Setup Local Environment Variables

You'll need to define a `.env` file in the root of your local project. You can use our [environment template file](https://github.com/microsoft/cobalt/blob/master/.env.template) to start. This will hold all the environment variables needed to run your Cobalt deployments locally.

1. Navigate to the root directory of your project and use the following command to copy the environment template file.

    ```bash
    cp .env.template .env
    ```

    ```bash
    $ tree infra
    ├───.env.template
    ├───.env ## NEW FILE GENERATED FROM THE COMMAND
    ├───modules
    │   └───providers
    │       ├───azure
    │       │   ├───...
    │       │   └───vnet
    │       └───common
    └───templates
        ├───az-hello-world
        │   └───test
        └───backend-state-setup
    ```

1. Provide values for the environment values in `.env` which are required to authenticate Terraform to provision resources within your subscription.

    ```bash
    # Versioning
    GO_VERSION=1.12.5
    TF_VERSION=0.12.2
    BUILD_BUILDID=1
    # Subscription Values
    ARM_SUBSCRIPTION_ID="<az-service-principal-subscription-id>"
    ARM_CLIENT_ID="<az-service-principal-client-id>"
    ARM_CLIENT_SECRET="<az-service-principal-auth-secret>"
    ARM_TENANT_ID="<az-service-principal-tenant>"
    ARM_ACCESS_KEY="<remote-state-storage-account-primary-key>"
    # Azure Blob Storage Values
    TF_VAR_remote_state_account="<tf-remote-state-storage-account-name>"
    TF_VAR_remote_state_container="<tf-remote-state-storage-container-name>"
    TF_WARN_OUTPUT_ERRORS=1
    TF_CLI_ARGS=
    ```

1. Execute the following commands to set up the environment variables for your bash session:

    ```bash
    # these commands setup all the environment variables needed to run this template
    DOT_ENV=<path to your .env file>
    export $(cat $DOT_ENV | xargs)
    ```

1. Execute the following login command to configure your local Azure CLI.

    ```bash
    # This logs your local Azure CLI in using the configured service principal.
    az login --service-principal -u $ARM_CLIENT_ID -p $ARM_CLIENT_SECRET --tenant $ARM_TENANT_ID
    ```

    ```bash
    # Other helpful azure cli commands
    az --version # Cobalt does not recommend a version but this command proves useful for troubleshooting the az cli
    az account show # A command for validating the azure cli is correctly configured
    az configure --defaults group='' # Use empty strings to clear default values for any sticky az properties like "group"
    ```

### **Step 4:** Initialize a Terraform Remote Environment

A Terraform environment is primarily any json file generated by the `terraform init` and/or `terraform workspace new` commands. Initializing a Terraform *remote* environment (as opposed to a local one) effectively means to create an Azure blob storage json file within your existing Azure blob storage account and container listed in the [prerequisites](#23-prerequisites).

* Navigate to the az-hello-world directory (i.e. ./infra/templates/az-hello-world) and execute the following commands to set up your remote terraform environment.

    ```bash
    # This configures terraform to leverage a remote backend that will help you and your
    # team keep consistent state
    terraform init -backend-config "storage_account_name=${TF_VAR_remote_state_account}" -backend-config "container_name=${TF_VAR_remote_state_container}"

    # This command configures terraform to use a workspace unique to you. This allows you to work
    # without stepping over your teammate's deployments
    terraform workspace new "az-hw-$USER" || terraform workspace select "az-hw-$USER"
    ```

    ```bash
    $ tree infra
    ├───.env.template
    ├───.env
    ├───modules
    └───templates
        ├───az-hello-world
        │   └───.terraform ## NEW LOCAL DIRECTORY GENERATED FROM INITIALIZING REMOTE STATE
        └───backend-state-setup
    ```

### **Step 5:** Deploy Cobalt's [_Azure Hello World CIT_](../infra/templates/az-hello-world/README.md)

This is the step that demonstrates what the [_Azure Hello World CIT_](../infra/templates/az-hello-world/README.md) has to offer by deploying and creating actual Azure cloud resources. This infrastructure template creates an [ARM Resource Group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview), an [App Service Plan](https://docs.microsoft.com/en-us/azure/app-service/overview-hosting-plans) and [App Service](https://docs.microsoft.com/en-us/azure/app-service/) running a public docker container. The previous step of initializing a Terraform environment from within the Azure Hello World CIT directory makes deployment possible.

1. From the az-hello-world directory, execute the following commands to orchestrate a deployment.

    ```bash
    # See what terraform will try to deploy without actually deploying
    terraform plan

    # Execute a deployment
    terraform apply
    ```

1. Take note of the name of the resource group output from Terraform apply.

* **Azure Hello World CIT [Main.tf](https://github.com/microsoft/cobalt/blob/master/infra/templates/az-hello-world/main.tf) File**

    A template is made up of custom modules and has several files. The main file that drives this template's deployment lives in the Main.tf file:

    ```HCL
    resource "azurerm_resource_group" "main" {
        name     = local.app_rg_name
        location = local.region
    }

    module "service_plan" {
        source              = "../../modules/providers/azure/service-plan"
        resource_group_name = azurerm_resource_group.main.name
        service_plan_name   = local.sp_name
    }

    module "app_service" {
        source                           = "../../modules/providers/azure/app-service"
        app_service_name_prefix          = local.app_svc_name_prefix
        service_plan_name                = module.service_plan.service_plan_name
        service_plan_resource_group_name = azurerm_resource_group.main.name
        docker_registry_server_url       = local.reg_url
        app_service_config               = local.app_services
    }
    ```

### **Step 6:** Validate Infrastructure Deployed Successfully

If you correctly completed the previous step, you can trust that Azure resources are now living in Azure. However, let's take it a step further and get your eyes on the infrastructure that was just deployed. Seeing the end result gives you the full quick start experience and is something that should be done for all future Cobalt CIT deployments.

1. Login to the Azure Portal.
1. Search for "Resource Group" to find the Resource Group menu.
1. Find and Select the name of the Resource Group output from the deployment.
1. Select the App Service output from the deployment.
1. Select the "overview" tab.
1. Wait for the App Service "URL" link to display itself from within the menu and then visit it.

### **Final Step:** Teardown Infrastructure

Tearing down the infrastructure just created is as easy as running a terraform command within the scope of the same Terraform environment and workspace that created it. Gaining access to the scope of the workspace is as easy as navigating to the Cobalt CIT template directory holding a reference to the environment and workspace.

1. From within the Azure portal, visit the Azure blob storage json file (i.e. "az-hw-$USER") which remotely holds the state of your deployed Azure infrastructure and view it's contents. The next step will wipe out the contents of this file and delete the associated Azure infrastructure.

1. Locally, from the az-hello-world directory, execute the following command to teardown your deployment and delete your resources.

    ```bash
    # Destroy resources and tear down deployment. Only do this if you want to destroy your deployment.
    terraform destroy
    ```

1. From within the Azure portal, revisit the Azure blob storage json file (i.e. "az-hw-$USER")  and view it's contents. At this point, this file should have an empty state.

## Conclusion

This is a great opportunity to point out that steps 4-6 are effectively the *local* Cobalt developer workflow. (i.e. create/choose a template ---> init ---> workspace (local or remote) ---> apply ---> plan ---> destroy)

### **Recommended Next Step:** *[Cobalt Templating from Scratch](https://github.com/microsoft/cobalt/blob/master/docs/3_NEW_TEMPLATE.md).*
