# Terraform file to create VMs and associated resources.
When creating virtual machines using Terraform, there are several resources that are dependent and required to be created. The specific resources may vary depending on the cloud provider and the type of virtual machine.
For example, when creating a Linux VM in Azure using Terraform, the following resources are typically required:
1. Resource Group
2. Virtual Network
3. Subnet
4. Public IP Address
5. managed Disk
6. Network interface
7. VM image 

### here is the code in onefile.
```
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "3.82.0"
    }
  }
}

provider "azurerm" {
 
  tenant_id       = "1811019b-bf9d-4365-a3ed-9ce56c1e1595"  # Replace with your Azure AD tenant ID
  subscription_id = "fca6c7bf-ca9a-475f-b2d2-d0228ee4ca2e"  # Replace with your Azure subscription ID
  features {}
}

variable "vm_names" {
  type    = list(string)
  default = ["jenkins-master", "build-slave", "ansible"]
}

resource "azurerm_resource_group" "my-rg" {
  name     = "my-rg-resource-group"
  location = "East US"
}

resource "azurerm_virtual_network" "my-vnet" {
  name                = "my-vnet-virtual-network"
  resource_group_name = azurerm_resource_group.my-rg.name
  location            = azurerm_resource_group.my-rg.location
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "my-subnet1" {
  name                 = "my-subnet1-subnet"
  resource_group_name  = azurerm_resource_group.my-rg.name
  virtual_network_name = azurerm_virtual_network.my-vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

resource "azurerm_public_ip" "pubip" {
  for_each             = toset(var.vm_names)
  name                 = "pubip-public-ip-${each.value}"
  resource_group_name  = azurerm_resource_group.my-rg.name
  location             = azurerm_resource_group.my-rg.location
  allocation_method    = "Static"
}

resource "azurerm_network_interface" "dpnic" {
  for_each              = toset(var.vm_names)
  name                  = "dpnic-nic-${each.value}"
  location              = azurerm_resource_group.my-rg.location
  resource_group_name   = azurerm_resource_group.my-rg.name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.my-subnet1.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.pubip[each.value].id
  }
}

resource "azurerm_linux_virtual_machine" "ec2" {
  for_each              = toset(var.vm_names)
  name                  = "ec2-vm-${each.value}"
  resource_group_name   = azurerm_resource_group.my-rg.name
  location              = azurerm_resource_group.my-rg.location
  size                  = "Standard_DS1_v2"
  admin_username        = "CHOOSE A USERNAME"
  admin_password        = "CHOOSE A PASSWORD"
  disable_password_authentication = false  # Disable password authentication
  
  ##admin_ssh_key {
    ##computer_name  = "hostname-${each.value}"
  ##  admin_username = "CHOOSE A USER NAME"
  ##  public_key = file("~/.ssh/id_rsa.pub")
   ###### }
 ## }

  network_interface_ids = [
    azurerm_network_interface.dpnic[each.value].id,
  ]

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-jammy"
    sku       = "22_04-lts"
    version   = "latest"
  }
}

resource "azurerm_network_security_group" "devpjnsg" {
  name                = "devpj-nsg"
  location            = azurerm_resource_group.my-rg.location
  resource_group_name = azurerm_resource_group.my-rg.name

  security_rule {
    name                       = "test123"
    priority                   = 100
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "*"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }
}

resource "azurerm_subnet_network_security_group_association" "sn1join" {
  subnet_id                 = azurerm_subnet.my-subnet1.id
  network_security_group_id = azurerm_network_security_group.devpjnsg.id
}



```





