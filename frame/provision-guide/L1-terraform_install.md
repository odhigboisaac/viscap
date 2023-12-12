## Prepare Terraform Environment on Windows
As part of this, we should setup 
1. Terraform
2. VS Code
3. AzureCLI 

### Install Terraform 
1. Download terraform the latest version from [here](https://developer.hashicorp.com/terraform/downloads)
2. Setup environment variable
click on start --> search "edit the environment variables" and click on it  
Under the advanced tab, chose "Environment variables" --> under the system variables select path variable   
and add terraform location in the path variable. system variables --> select path  
add new --> terraform_Path   
in my system, this path location is C:\Program Files\terraform_1.3.7

1. Run the below command to validate terraform version
   ```sh
   terraform -version
   ```
   the output should be something like below 
   ```sh
   Terraform v1.3.7
   on windows_386
   ```

 ### Install Visual Studio code

  Download vs code latest version from [here](https://code.visualstudio.com/download) and install it.

### AzureCLI installation

Downlaod AzureCLI latest ver from [here](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli)


Configure credentials
   ```sh
   az configure 
   ```

### Some of the basic Terraform CLI commands include:
1. terraform init: Prepare your working directory for other commands.
2. terraform validate: Check whether the configuration is valid.
3. terraform plan: Show changes required by the current configuration.
4. terraform apply: Create or update infrastructure.
5. terraform destroy: Destroy previously-created infrastructure.
6. terraform fmt: Reformat your configuration in the standard style.
7. terraform state: Interact with Terraform state.
9. terraform import: is used to bring existing resources into Terraform state.
10. workspace: Workspace management