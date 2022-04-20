- [Part 1: Prerequisites for Azure administrators](#part-1-prerequisites-for-azure-administrators)
  - [Use Azure Cloud Shell](#use-azure-cloud-shell)
  - [Azure Cloud Shell features](#azure-cloud-shell-features)
  - [Use Azure PowerShell](#use-azure-powershell)
  - [What is the Az module?](#what-is-the-az-module)
  - [Required resources for IaaS Virtual Machines](#required-resources-for-iaas-virtual-machines)
    - [Start with the network](#start-with-the-network)
    - [Segregate your network](#segregate-your-network)
    - [Secure the network](#secure-the-network)
    - [Plan each VM deployment](#plan-each-vm-deployment)
    - [Name the VM](#name-the-vm)
    - [What is an Azure resource?](#what-is-an-azure-resource)
    - [Decide the location for the VM](#decide-the-location-for-the-vm)
    - [Determine the size of the VM](#determine-the-size-of-the-vm)
    - [Understanding the pricing model](#understanding-the-pricing-model)
    - [Storage for the VM](#storage-for-the-vm)
    - [What is Azure Storage?](#what-is-azure-storage)
    - [Select an operating system](#select-an-operating-system)
  - [Azure Resource Manager](#azure-resource-manager)
  - [What are Resource Manager templates?](#what-are-resource-manager-templates)
  - [Azure PowerShell](#azure-powershell)
  - [Azure CLI](#azure-cli)
  - [Programmatic (APIs)](#programmatic-apis)
  - [Azure REST API](#azure-rest-api)
  - [Azure Client SDK](#azure-client-sdk)
  - [Azure VM extensions](#azure-vm-extensions)
  - [Azure Automation services](#azure-automation-services)
  - [What is availability?](#what-is-availability)
  - [Why do I need to think about availability when using Azure?](#why-do-i-need-to-think-about-availability-when-using-azure)
  - [What is an availability set?](#what-is-an-availability-set)
    - [What is a fault domain?](#what-is-a-fault-domain)
    - [What is an update domain?](#what-is-an-update-domain)
  - [Failover across locations](#failover-across-locations)
  - [Back up your virtual machines](#back-up-your-virtual-machines)
  - [Advantages of using Azure Backup](#advantages-of-using-azure-backup)
  - [Use Azure Backup](#use-azure-backup)
  - [Consistent management layer](#consistent-management-layer)
  - [Benefits](#benefits)
  - [Guidance](#guidance)
  
# Part 1: Prerequisites for Azure administrators

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

--- 

## Required resources for IaaS Virtual Machines

- Start with the network
- Name the VM
- Decide the location for the VM
- Determine the size of the VM
- Understanding the pricing model
- Storage for the VM
- Select an operating system

### Start with the network
The first thing you should think about isn't the virtual machine at all - it's the network. Virtual networks are used in Azure to provide private connectivity between Azure Virtual Machines and other Azure services. VMs and services that are part of the same virtual network can access one another. By default, services outside the virtual network cannot connect to services within the virtual network.

You can, however, configure the network to allow access to the external service, including your on-premises servers. This latter point is why you should spend some time thinking about your network configuration. Network addresses and subnets are not trivial to change once you have them set up, and if you plan to connect your private company network to the Azure services, you will want to make sure you consider the topology before putting any VMs into place. When you set up a virtual network, you specify the available address spaces, subnets, and security.

This is the range of private addresses that the VMs and services in your network can use.

### Segregate your network
After deciding the virtual network address space(s), you can create one or more subnets for your virtual network. You do this to break up your network into more manageable sections. For example, you might assign 10.1.0.0 to VMs, 10.2.0.0 to back-end services, and 10.3.0.0 to SQL Server VMs.

### Secure the network
By default, there is no security boundary between subnets, so services in each of these subnets can talk to one another. However, you can set up Network Security Groups (NSGs), which allow you to control the traffic flow to and from subnets and to and from VMs. NSGs act as software firewalls, applying custom rules to each inbound or outbound request at the network interface and subnet level. This allows you to fully control every network request coming in or out of the VM.

### Plan each VM deployment
Once you have mapped out your communication and network requirements, you can start thinking about the VMs you want to create. A good plan is to select a server and take an inventory:

- What does the server communicate with?
- Which ports are open?
- Which OS is used?
- How much disk space is in use?
- What kind of data does this use? Are there restrictions (legal or otherwise) with not having it on-premises?
- What sort of CPU, memory, and disk I/O load does the server have? Is there burst traffic to account for?

We can then start to answer some of the questions Azure will have for a new virtual machine.

### Name the VM
One piece of information people often don't put much thought into is the name of the VM. The VM name is used as the computer name, which is configured as part of the operating system. You can specify a name of up to 15 characters on a Windows VM and 64 characters on a Linux VM.

This name also defines a manageable Azure resource, and it's not trivial to change later. That means you should choose names that are meaningful and consistent, so you can easily identify what the VM does. A good convention is to include the following information in the name:
![Screenshot 2022-04-19 142317](https://user-images.githubusercontent.com/87706066/164014136-e8ffc5a2-c3c3-458c-925d-0ec5cd4d494c.png)

### What is an Azure resource?
An Azure resource is a manageable item in Azure. Just like a physical computer in your datacenter, VMs have several elements that are needed to do their job:

- The VM itself
- Storage account for the disks
- Virtual network (shared with other VMs and services)
- Network interface to communicate on the network
- Network Security Group(s) to secure the network traffic
- Public Internet address (optional)

Azure will create all of these resources if necessary, or you can supply existing ones as part of the deployment process. Each resource needs a name that will be used to identify it. If Azure creates the resource, it will use the VM name to generate a resource name - another reason to be very consistent with your VM names!

### Decide the location for the VM
Azure has datacenters all over the world filled with servers and disks. These datacenters are grouped into geographic regions ('West US', 'North Europe', 'Southeast Asia', etc.) to provide redundancy and availability.

When you create and deploy a virtual machine, you must select a region where you want the resources (CPU, storage, etc.) to be allocated. This lets you place your VMs as close as possible to your users to improve performance and to meet any legal, compliance, or tax requirements.

Two other things to think about regarding the location choice. First, the location can limit your available options. Each region has different hardware available and some configurations are not available in all regions. Second, there are price differences between locations. If your workload isn't bound to a specific location, it can be very cost effective to check your required configuration in multiple regions to find the lowest price.

### Determine the size of the VM
Once you have the name and location set, you need to decide on the size of your VM. Rather than specify processing power, memory, and storage capacity independently, Azure provides different VM sizes that offer variations of these elements in different sizes. Azure provides a wide range of VM size options allowing you to select the appropriate mix of compute, memory, and storage for what you want to do.

The best way to determine the appropriate VM size is to consider the type of workload your VM needs to run. Based on the workload, you're able to choose from a subset of available VM sizes. Workload options are classified as follows on Azure:

![Screenshot 2022-04-19 145135](https://user-images.githubusercontent.com/87706066/164019643-dd04213f-0625-4a25-a1c4-5021bfbe5c1e.png)

You're able to filter on the workload type when you configure the VM size in the Azure. The size you choose directly affects the cost of your service. The more CPU, memory, and GPU you need, the higher the price point.

### Understanding the pricing model
There are two separate costs the subscription will be charged for every VM: compute and storage. By separating these costs, you scale them independently and only pay for what you need.

**Compute costs** - Compute expenses are priced on a per-hour basis but billed on a per-minute basis. For example, you are only charged for 55 minutes of usage if the VM is deployed for 55 minutes. You are not charged for compute capacity if you stop and deallocate the VM since this releases the hardware. The hourly price varies based on the VM size and OS you select. The cost for a VM includes the charge for the Windows operating system. Linux-based instances are cheaper because there is no operating system license charge.

**Storage costs** - You are charged separately for the storage the VM uses. The status of the VM has no relation to the storage charges that will be incurred; even if the VM is stopped/deallocated and you aren’t billed for the running VM, you will be charged for the storage used by the disks.

You're able to choose from two payment options for compute costs.

### Storage for the VM
Best practice is that all Azure virtual machines will have at least two virtual hard disks (VHDs). The first disk stores the operating system, and the second is used as temporary storage. You can add additional disks to store application data; the maximum number is determined by the VM size selection (typically two per CPU). It's common to create one or more data disks, particularly since the OS disk tends to be quite small. Also, separating out the data to different VHDs allows you to manage the security, reliability, and performance of the disk independently.

The data for each VHD is held in Azure Storage as page blobs, which allows Azure to allocate space only for the storage you use. It's also how your storage cost is measured; you pay for the storage you are consuming.

### What is Azure Storage?
Azure Storage is Microsoft's cloud-based data storage solution. It supports almost any type of data and provides security, redundancy, and scalable access to the stored data. A storage account provides access to objects in Azure Storage for a specific subscription. VMs always have one or more storage accounts to hold each attached virtual disk.

Virtual disks can be backed by either **Standard** or **Premium** Storage accounts. Azure Premium Storage leverages solid-state drives (SSDs) to enable high performance and low latency for VMs running I/O-intensive workloads. Use Azure Premium Storage for production workloads, especially those that are sensitive to performance variations or are I/O intensive. For development or testing, Standard storage is fine.

When you create disks, you will have two options for managing the relationship between the storage account and each VHD. You can choose either **unmanaged** disks or **managed** disks.

### Select an operating system
Azure provides a variety of OS images that you can install into the VM, including several versions of Windows and flavors of Linux. If you are looking for more than just base OS images, you can search the Azure Marketplace for more sophisticated install images that include the OS and popular software tools installed for specific scenarios. Instead of setting up and configuring each component, you can leverage a Marketplace image and install the entire stack all at once.
Finally, if you can't find a suitable OS image, you can create your disk image with what you need, upload it to Azure storage, and use it to create an Azure VM.

## Azure Resource Manager
Typically, your Azure infrastructure will contain many resources, many of them related to one another in some way. Azure Resource Manager makes working with these related resources more efficient. It organizes resources into named resource groups that let you deploy, update, or delete all of the resources together. When we created the Ubuntu VM site, we identified the resource group as part of the VM creation, and Resource Manager placed the associated resources into the same group.


## What are Resource Manager templates?
**Resource Manager templates** are JSON files that define the resources you need to deploy for your solution.

You can create a resource template for your VM. From the VM menu, under **Automation** select **Export template**.

## Azure PowerShell
Creating administration scripts is a powerful way to optimize your workflow. You can automate everyday, repetitive tasks, and after a script has been verified, it will run consistently, likely reducing errors. Azure PowerShell is ideal for one-off interactive tasks and/or the automation of repeated tasks.

For example, you can use the `New-AzVM` cmdlet to create a new Azure virtual machine.

```powershell
New-AzVm `
    -ResourceGroupName "TestResourceGroup" `
    -Name "test-wp1-eus-vm" `
    -Location "East US" `
    -VirtualNetworkName "test-wp1-eus-network" `
    -SubnetName "default" `
    -SecurityGroupName "test-wp1-eus-nsg" `
    -PublicIpAddressName "test-wp1-eus-pubip" `
    -OpenPorts 80,3389
```

## Azure CLI
Another option for scripting and command-line Azure interaction is the Azure CLI.

The Azure CLI is Microsoft's cross-platform command-line tool for managing Azure resources such as virtual machines and disks from the command line. It's available for Windows, Linux and macOS, or in a browser using the Cloud Shell. Like Azure PowerShell, the Azure CLI is a powerful way to streamline your administrative workflow. Unlike Azure PowerShell, the Azure CLI does not need PowerShell to function.

For example, from the CLI, you can create an Azure VM with the `az vm create` command.
```azurecli
az vm create \
    --resource-group TestResourceGroup \
    --name test-wp1-eus-vm \
    --image win2016datacenter \
    --admin-username jonc \
    --admin-password aReallyGoodPasswordHere
```
The Azure CLI can be used with other scripting languages, such as Ruby and Python. Both languages are commonly used on non-Windows-based machines where a developer might not be familiar with PowerShell.

## Programmatic (APIs)
Generally speaking, both Azure PowerShell and Azure CLI are good options if you have simple scripts to run and want to stick to command-line tools. When it comes to more complex scenarios, where the creation and management of VMs form part of a larger application with complex logic, another approach is needed.

## Azure REST API
The Azure REST API provides developers with operations categorized by resource as well as the ability to create and manage VMs. Operations are exposed as URIs with corresponding HTTP methods (`GET`, `PUT`, `POST`, `DELETE`, and `PATCH`) and a corresponding response.

The Azure Compute APIs give you programmatic access to virtual machines and their supporting resources. With this API, you have operations to:

- Create and manage availability sets
- Add and manage virtual machine extensions
- Create and manage managed disks, snapshots, and images
- Access the platform images available in Azure
- Retrieve usage information of your resources
- Create and manage virtual machines
- Create and manage virtual machine scale sets


You can interact with every type of resource in Azure programmatically.

## Azure Client SDK
Even though the REST API is platform and language agnostic, most often developers will look toward a higher level of abstraction. The Azure Client SDK encapsulates the Azure REST API, making it much easier for developers to interact with Azure.

The Azure Client SDKs are available for a variety of languages and frameworks, including .NET-based languages such as C#, Java, Node.js, PHP, Python, Ruby, and Go.

Here's an example snippet of C# code to create an Azure VM using the `Microsoft.Azure.Management.Fluent` NuGet package.

```csharp
var azure = Azure
    .Configure()
    .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
    .Authenticate(credentials)
    .WithDefaultSubscription();
// ...
var vmName = "test-wp1-eus-vm";

azure.VirtualMachines.Define(vmName)
    .WithRegion(Region.USEast)
    .WithExistingResourceGroup("TestResourceGroup")
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("jonc")
    .WithAdminPassword("aReallyGoodPasswordHere")
    .WithComputerName(vmName)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```
Here's the same snippet in Java using the Azure Java SDK.
``` java
String vmName = "test-wp1-eus-vm";
// ...
VirtualMachine virtualMachine = azure.virtualMachines()
    .define(vmName)
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("TestResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("jonc")
    .withAdminPassword("aReallyGoodPasswordHere")
    .withComputerName(vmName)
    .withSize("Standard_DS1")
    .create();
```
## Azure VM extensions
Let's assume you want to configure and install additional software on your virtual machine after the initial deployment. You want this task to use a specific configuration, monitored and executed automatically.

Azure VM extensions are small applications that enable you to configure and automate tasks on Azure VMs after initial deployment. Azure VM extensions can be run with the Azure CLI, PowerShell, Azure Resource Manager templates, and the Azure portal.

You bundle extensions with a new VM deployment, or run them against an existing system.

## Azure Automation services
Saving time, reducing errors, and increasing efficiency are some of the most significant operational management challenges faced when managing remote infrastructure. If you have a lot of infrastructure services, you might want to consider using higher-level services in Azure to help you operate from a higher level.

**Azure Automation** enables you to integrate services that allow you to automate frequent, time-consuming, and error-prone management tasks with ease. These services include **process automation**, **configuration management**, and **update management**.


- **Process Automation**. Let's assume you have a VM that is monitored for a specific error event. You want to take action, and fix the problem as soon as it's reported. Process automation enables you to set up watcher tasks that can respond to events that may occur in your datacenter.
- **Configuration Management**. Perhaps you want to track software updates that become available for the operating system that runs on your VM. There are specific updates you may want to include or exclude. Configuration management enables you to track these updates, and take action as required. You use Microsoft Endpoint Configuration Manager to manage your company's PC, servers, and mobile devices. You can extend this support to your Azure VMs with Configuration Manager.
- **Update Management**. This is used to manage updates and patches for your VMs. With this service, you're able to assess the status of available updates, schedule installation, and review deployment results to verify updates applied successfully. Update management incorporates services that provide process and configuration management. You enable update management for a VM directly from your Azure Automation account. You can also enable update management for a single virtual machine from the virtual machine pane in the portal.

## What is availability?
Availability is the percentage of time a service is available for use.

Let's assume you have a website, and you want your customers to be able to access information at all times. Your expectation is 100% availability concerning website access.

## Why do I need to think about availability when using Azure?
Azure VMs run on physical servers hosted within the Azure Datacenter. As with most physical devices, there's a chance that there could be a failure. If the physical server fails, the virtual machines hosted on that server will also fail. If this happens, Azure will move the VM to a healthy host server automatically. However, this self-healing migration could take several minutes, during which, the application(s) hosted on that VM will not be available.

The VMs could also be affected by periodic updates initiated by Azure itself. These maintenance events range from software updates to hardware upgrades and are required to improve platform reliability and performance. These events usually are performed without impacting any guest VMs, but sometimes the virtual machines will be rebooted to complete an update or upgrade.

To ensure your services aren't interrupted and avoid a single point of failure, it's recommended to deploy at least two instances of each VM. This feature is called an availability set.

## What is an availability set?
An availability set is a logical feature used to ensure that a group of related VMs are deployed so that they aren't all subject to a single point of failure and not all upgraded at the same time during a host operating system upgrade in the datacenter. VMs placed in an availability set should perform an identical set of functionalities and have the same software installed.

You can create availability sets through the Azure portal in the disaster recovery section. Also, you can build them using Resource Manager templates, or any of the scripting or API tools. When you place VMs into an availability set, Azure guarantees to spread them across **Fault Domains** and **Update Domains**.

### What is a fault domain?
A fault domain is a logical group of hardware in Azure that shares a common set of hardware components, and that share a single point of failure. You can think of it as a rack within an on-premises datacenter. The first two VMs in an availability set will be provisioned into **two different racks** so that if the network or the power failed in a rack, only one VM would be affected. Fault domains are also defined for managed disks attached to VMs.

### What is an update domain?
An update domain is a logical group of hardware that can undergo maintenance, or be rebooted at the same time. Azure will automatically place availability sets into update domains to minimize the impact when the Azure platform introduces host operating system changes. Azure then processes each update domain one at a time.

Availability sets are a powerful feature to ensure the services running in your VMs are always available to your customers. However, they aren't foolproof. What if something happens to the data or the software running on the VM itself? For that, we'll need to look at other disaster recovery and backup techniques.

## Failover across locations
You can also replicate your infrastructure across sites to handle regional failover. Azure Site Recovery replicates workloads from a primary site to a secondary location. If an outage happens at your primary site, you can fail over to a secondary location. This failover enables users to continue to access your applications without interruption. You can then fail back to the primary location after it's up and running again. Azure Site Recovery is about replication of virtual or physical machines; it keeps your workloads available in an outage.

While there are many attractive technical features to Site Recovery, there are at least two significant business advantages:

- Site Recovery enables the use of Azure as a destination for recovery, thus eliminating the cost and complexity of maintaining a secondary physical datacenter.
- Site Recovery makes it incredibly simple to test failovers for recovery drills without impacting production environments. This makes it easy to test your planned or unplanned failovers. After all, you don’t have a good disaster recovery plan if you’ve never tried to failover.

The recovery plans you create with Site Recovery can be as simple or as complex as your scenario requires. You can leverage the recovery plans to replicate workloads to Azure, easily enabling new opportunities for migration, temporary bursts during surge periods, or development and testing of new applications.

## Back up your virtual machines
Azure Backup can be used for a wide range of data backup scenarios, such as:

- Files and folders on Windows OS machines (physical or virtual, local or cloud)
- Application-aware snapshots (Volume Shadow Copy Service)
- Popular Microsoft server workloads such as Microsoft SQL Server, Microsoft SharePoint, and Microsoft Exchange
- Native support for Azure Virtual Machines, both Windows, and Linux
- Linux and Windows 10 client machines

## Advantages of using Azure Backup
Traditional backup solutions don't always take full advantage of the underlying Azure platform. The result is a solution that tends to be expensive or inefficient. The solution either offers too much or too little storage, does not offer the correct types of storage, or has cumbersome and long-winded administrative tasks. Azure Backup was designed to work in tandem with other Azure services and provides several distinct benefits.

- **Automatic storage management**. Azure Backup automatically allocates and manages backup storage and uses a pay-as-you-use model. You only pay for what you use.
- **Unlimited scaling**. Azure Backup uses the power and scalability of Azure to deliver high availability.
- **Multiple storage options**. Azure Backup offers locally redundant storage where all copies of the data exist within the same region and geo-redundant storage where your data is replicated to a secondary region.
- **Unlimited data transfer**. Azure Backup does not limit the amount of inbound or outbound data you transfer. Azure Backup also does not charge for the data that is transferred.
- **Data encryption**. Data encryption allows for secure transmission and storage of your data in Azure.
- **Application-consistent backup**. An application-consistent backup means that a recovery point has all required data to restore the backup copy. Azure Backup provides application-consistent backups.
- **Long-term retention**. Azure doesn't limit the length of time you keep the backup data.

## Use Azure Backup
Azure Backup uses several components that you download and deploy to each computer you want to back up. The component that you deploy depends on what you want to protect.

- Azure Backup agent
- System Center Data Protection Manager
- Azure Backup Server
- Azure Backup VM extension

Azure Backup uses a Recovery Services vault for storing the backup data. A vault is backed by Azure Storage blobs, making it a very efficient and economical long-term storage medium. With the vault in place, you can select the machines to back up, and define a backup policy (when snapshots are taken and for how long they’re stored).

---

## Consistent management layer 
Azure Resource Manager provides a consistent management layer to perform tasks through Azure PowerShell, Azure CLI, Azure portal, REST API, and client SDKs. Choose the tools and APIs that work best for you.

The following image shows how all the tools interact with the same Azure Resource Manager API. The API passes requests to the Azure Resource Manager service, which authenticates and authorizes the requests. Azure Resource Manager then routes the requests to the appropriate resource providers.

![resource-manager-016a1bac](https://user-images.githubusercontent.com/87706066/164200629-bdfe1da2-d502-4132-bcde-3a4d15f65e04.png)


## Benefits

Azure Resource Manager provides several benefits:

- You can deploy, manage, and monitor all the resources for your solution as a group, rather than handling these resources individually.
- You can repeatedly deploy your solution throughout the development lifecycle and have confidence your resources are deployed in a consistent state.
- You can manage your infrastructure through declarative templates rather than scripts.
- You can define the dependencies between resources so they're deployed in the correct order.
- You can apply access control to all services in your resource group because Role-Based Access Control (RBAC) is natively integrated into the management platform.
- You can apply tags to resources to logically organize all the resources in your subscription.
- You can clarify your organization's billing by viewing costs for a group of resources sharing the same tag.

## Guidance

The following suggestions help you take full advantage of Azure Resource Manager when working with your solutions.


- Define and deploy your infrastructure through the declarative syntax in Azure Resource Manager templates, rather than through imperative commands.
- Define all deployment and configuration steps in the template. You should have no manual steps for setting up your solution.
- Run imperative commands to manage your resources, such as to start or stop an app or machine.
- Arrange resources with the same lifecycle in a resource group. Use tags for all other organizing of resources.

