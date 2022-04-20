- [Part1: Prerequisites for Azure administrators](#part1-prerequisites-for-azure-administrators)
  - [Use Azure Cloud Shell](#use-azure-cloud-shell)
  - [Azure Cloud Shell features](#azure-cloud-shell-features)
  - [Use Azure PowerShell](#use-azure-powershell)
  - [What is the Az module?](#what-is-the-az-module)
  - [Use Azure CLI](#use-azure-cli)
  
# Part1: Prerequisites for Azure administrators

## Use Azure Cloud Shell
Azure Cloud Shell is an interactive, browser-accessible shell for managing Azure resources. It provides the flexibility of choosing the shell experience that best suits the way you work. Linux users can opt for a Bash experience, while Windows users can opt for PowerShell.

Cloud Shell enables access to a browser-based command-line experience built with Azure management tasks in mind. You can use Cloud Shell to work untethered from a local machine in a way only the cloud can provide.

## Azure Cloud Shell features

- Is temporary and requires a new or existing Azure Files share to be mounted.
- Offers an integrated graphical text editor based on the open-source Monaco Editor.
- Authenticates automatically for instant access to your resources.
- Runs on a temporary host provided on a per-session, per-user basis.
- Times out after 20 minutes without interactive activity.
- Requires a resource group, storage account, and Azure File share.
- Uses the same Azure file share for both Bash and PowerShell.
- Is assigned to one machine per user account.
- Persists $HOME using a 5-GB image held in your file share.
- Permissions are set as a regular Linux user in Bash.

## Use Azure PowerShell
Azure PowerShell is a module that you add to Windows PowerShell or PowerShell Core to enable you to connect to your Azure subscription and manage resources. Azure PowerShell requires PowerShell to function. PowerShell provides services such as the shell window and command parsing. Azure PowerShell adds the Azure-specific commands.

For example, Azure PowerShell provides the New-AzVm command that creates a virtual machine inside your Azure subscription. To use it, you would launch the PowerShell application and then issue a command such as the following command:
```powershell
New-AzVm `
    -ResourceGroupName "CrmTestingResourceGroup" `
    -Name "CrmUnitTests" `
    -Image "UbuntuLTS"
    ...
```
Azure PowerShell is also available two ways: inside a browser via the Azure Cloud Shell, or with a local installation on Linux, macOS, or the Windows operating system. In both cases, you have two modes from which to choose: you can use it in interactive mode in which you manually issue one command at a time, or in scripting mode where you execute a script that consists of multiple commands.

## What is the Az module?
Az is the formal name for the Azure PowerShell module containing cmdlets to work with Azure features. It contains hundreds of cmdlets that let you control nearly every aspect of every Azure resource. You can work with the following features, and more:

- Resource groups
- Storage
- VMs
- Azure AD
- Containers
- Machine learning

## Use Azure CLI
Azure CLI is a command-line program to connect to Azure and execute administrative commands on Azure resources. It runs on Linux, macOS, and Windows, and allows administrators and developers to execute their commands through a terminal, command-line prompt, or script instead of a web browser. For example, to restart a VM, you would use a command such as the following:

```azurecli
az vm restart -g MyResourceGroup -n MyVm
```

Azure CLI lets you control nearly every aspect of every Azure resource. You can work with resource groups, storage, VMs, Azure Active Directory (Azure AD), containers, machine learning, and so on.

Commands in the CLI are structured in groups and subgroups. Each group represents a service provided by Azure, and the subgroups divide commands for these services into logical groupings. For example, the `storage` group contains subgroups including account, blob, share, and queue.

So, how do you find the particular commands you need? One way is to use `az find`. For example, if you want to find commands that might help you manage a storage blob, you can use the following find command:
```azurecli
az find blob
```
If you already know the name of the command you want, the `--help` argument for that command will get you more detailed information on the command, and for a command group, a list of the available subcommands. For example, here's how you can get a list of the subgroups and commands for managing blob storage:
```azurecli
az storage blob --help
```
---