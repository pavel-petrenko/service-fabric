# Microsoft Azure Service Fabric 9.1 Cumulative Update 6.0 Release Notes

This release will only be available through manual upgrades. Clusters set to automatic upgrades will not receive this release. For how to configure upgrades, please see [classic](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-upgrade) or [managed](https://docs.microsoft.com/en-us/azure/service-fabric/how-to-managed-cluster-configuration) documentation.

## Contents
* [Service Fabric Packages and Versions](#service-fabric-packages-and-versions)
* [Service Fabric Feature and Bug Fixes](#service-fabric-feature-and-bug-fixes)
* [Retirement and Deprecation Path Callouts](#retirement-and-deprecation-path-callouts)
* [Repositories and Download Links](#repositories-and-download-links)

## Service Fabric Packages and Versions
The following packages and versions are part of this release:

| Service | Platform | Version |
|---------|----------|---------|
|Service Fabric Runtime| Ubuntu 18 <br> Ubuntu 20 <br> Windows | 9.1.1642.1 <br> 9.1.1642.1 <br> 9.1.1851.9590 |
|Service Fabric for Windows Server|Service Fabric Standalone Installer Package | 9.1.1851.9590 |
|.NET SDK |Windows .NET SDK <br> Microsoft.ServiceFabric <br> Reliable Services and Reliable Actors <br> ASP.NET Core Service Fabric integration| 6.1.1851  <br> 9.1.1851 <br> 6.1.1851 <br> 6.1.1851 |
|Java SDK  |Java for Linux SDK  | 1.0.6 |
|Service Fabric PowerShell and CLI | AzureRM PowerShell Module  <br> SFCTL |  0.3.15  <br> 11.0.1 |

## Service Fabric Feature and Bug Fixes
| Versions | Issue Type | Description | Resolution | 
|-|-|-|-|
| **Windows - 9.1.1851.9590<br>Ubuntu 18 - 9.1.1642.1<br>Ubuntu 20 - 9.1.1642.1** | **Bug** | Backup Restore Service (BRS) | **Brief Description**: Partitions with backup protection may revert to a past restored state upon experiencing data loss. This issue may cause partitions to restore themselves to an unintended or outdated state without user consent or notification and might result in data loss, inconsistency, and corruption, affecting the system's usability and functionality. <br>**Solution**: To address this, partition restore will only occur when an active restore operation has been initiated. If a backup-protected partition encounters data loss, it will wait for user intervention before restoring its state.
| **Windows - 9.1.1851.9590<br>Ubuntu 18 - 9.1.1642.1<br>Ubuntu 20 - 9.1.1642.1** | **Bug** | Backup Restore Service (BRS) | **Brief Description**: Presently, in the process of deleting expired backups from the backup store, Backup Restore Service (BRS) attempts to retrieve all backups before initiating the deletion of expired backups. However, this approach can encounter repeated failures if the Backup Store is facing pressure and unable to provide all backups to BRS at once. Consequently, this situation prevents BRS from deleting any expired backups, resulting in a continuous accumulation of backup files.  <br>**Solution**: BRS has been updated to perform the removal of expired backups in batches. The process involves the sequential processing of backups within distinct batches housed in the Backup Store. Within each batch, expired backups will be deleted prior to advancing to the subsequent batch.
| **Windows - 9.0.1851.9590<br>Ubuntu 18 - 9.0.1642.1<br>Ubuntu 20 - 9.0.1642.1** | **Bug** | Backup Restore Service (BRS) | **Brief Description**: The "GetBackups" API of Backup Restore Service (BRS), operates asynchronously. However, a part of the API makes a synchronous call to enumerate backup data from Azure Blob Storage which would continue retrieving backups even if the client has timed out. This could result in long-running threads in BRS service especially when it is under load. <br>**Solution**: The fix addresses this issue by converting the Azure Blob Storage call to an asynchronous operation ensuring proper enforcement of API timeouts. As a result, the performance of the GetBackups API is improved significantly.
| **Windows - 9.1.1851.9590<br>Ubuntu 18 - 9.1.1642.1<br>Ubuntu 20 - 9.1.1642.1**| **Bug** | Backup Restore Service (BRS) | **Brief Description**: When a replica with scheduled backup configured using Backup Restore Service (BRS) encounters OnDataLossAsync, it waits for user intervention to become available. However, this state is not communicated accurately to users through events, health reports, or exception messages. <br>**Solution**: This has been addressed by adding events and exception messages thereby providing an accurate description of the state of the replica to the user along with suggesting steps to make the replica healthy. 
| **Windows - 9.1.1851.9590<br>Ubuntu 18 - 9.1.1642.1<br>Ubuntu 20 - 9.1.1642.1** | **Feature** | Reliable Collection (RC) | **Brief Description**: Enumeration operation in Reliable Collection (RC) occasionally throws ArgumentException, impacting current managed RC users that use enumeration. <br>**Solution**: The issue has been addressed by relaxing the constraint on the internal data structure to allow duplicated keys in a non-steady state.<br>**Workaround**: Implement a try-catch block to retry the enumeration operation with exponential backoff.
| **Windows - 9.1.1851.9590<br>Ubuntu 18 - 9.1.1642.1<br>Ubuntu 20 - 9.1.1642.1** | **Bug** | FabricClient Constructor | **Brief Description**: The FabricClient constructor addresses a scenario where the provided parameter "hostEndpoints" is empty or null. When the constructor is used in this way, it is assumed that the code is running inside the cluster, and the FabricClient instance will connect to the cluster via the local Gateway service on the same machine using the provided security credentials. <br>**Solution**: Customers can now create a FabricClient instance in their applications that can connect to the local cluster with custom security credentials. The Service Fabric SDK will internally determine the local Gateway endpoint, similar to the behavior of the parameter-less FabricClient constructor. This enhancement provides more flexibility and control for connecting to the local cluster with specific security settings. <br>**Documentation Reference**: [Fabric Client Constructor](https://learn.microsoft.com/en-us/dotnet/api/system.fabric.fabricclient.-ctor?view=azure-dotnet#system-fabric-fabricclient-ctor(system-fabric-securitycredentials-system-string()))
| **Windows - 9.1.1851.9590<br>Ubuntu 18 - 9.1.1642.1<br>Ubuntu 20 - 9.1.1642.1** | **Bug** | Diagnostics | **Brief Description**: The DEB package for Ubuntu 18.04 and 20.04 have been updated to include "lttng-tools" as a dependency of Service Fabric, and the "lttng-modules-dkms" package has been removed as a dependency. Due to the optional nature of the "lttng-tools" package in the "lttng-modules-dkms" dependency, there was a possibility that "lttng-tools" would not be installed with Service Fabric. This led to the malfunctioning of internal Service Fabric traces without "lttng-tools". <br>**Solution**: To address this the "lttng-tools" package has been added as a dependency to the Service Fabric package.


## Retirement and Deprecation Path Callouts
* Ubuntu 18.04 LTS has reached its 5-year end-of-life window on June-2023. Service Fabric runtime has dropped support for 18.04 LTS after the published date, and we recommend moving your clusters and applications to supported versions listed here: [Service Fabric supported Linux versions](https://learn.microsoft.com/en-us/azure/service-fabric/service-fabric-versions#supported-linux-versions-and-support-end-date)
* Service Fabric runtime will soon stop supporting BinaryFormatter based remoting exception serialization by default and move to using Data Contract Serialization (DCS) based remoting exception serialization by default. Current applications using it will continue to work as-is, but Service Fabric strongly recommends moving to using Data Contract Serialization (DCS) based remoting exception instead.
* Previously communicated, Service Fabric runtime had planned to remove Service Fabric runtime version 6.4 packages and older, as well as SDK version 3.3 packages and older, from the package Download Center in July 2023. We would like to inform you that this timeline has been extended, and the removal will now take place in January 2024.
* Service Fabric runtime will soon be archiving and removing Service Fabric runtime versions less than 7.2 and older, as well as the corresponding SDK version 4.2 packages and older from the package Download Center. Archiving/Removing will affect application scaling and re-imaging of virtual machines in a Service Fabric Cluster running on unsupported versions. After older versions are removed/archived, this may cause failure while rolling back when the current in-progress upgrade has errors.<br>
To prevent disruption of workloads, create a new cluster using the following steps:<ul><li>[Create a Service Fabric cluster using ARM template](https://learn.microsoft.com/en-us/azure/service-fabric/quickstart-cluster-template)</li><li>[Create a Standalone cluster](https://learn.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-for-windows-server)</li></ul> Install the supported version of Service Fabric SDK based on the Runtime version installed on the cluster.


## Repositories and Download Links
The table below is an overview of the direct links to the packages associated with this release. 
Follow this guidance for setting up your developer environment: 
* [Getting Started with Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux)
* [Getting Started with Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac)
* [Getting Started with Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

| Area | Package | Version | Repository | Direct Download Link |
|-|-|-|-|-|
|Service Fabric Runtime |Ubuntu Developer Set-up | 9.1.1642.1 |N/A | Cluster Runtime: https://apt-mo.trafficmanager.net/repos/servicefabric/pool/main/s/servicefabric <br> Service Fabric SDK for local cluster setup: https://apt-mo.trafficmanager.net/repos/servicefabric/pool/main/s/servicefabricsdkcommon/ <br> Container image: https://hub.docker.com/r/microsoft/service-fabric-onebox/ 
|| Windows Developer Set-up| 9.1.1851.9590 | N/A | https://download.microsoft.com/download/b/8/a/b8a2fb98-0ec1-41e5-be98-9d8b5abf7856/MicrosoftServiceFabric.9.1.1851.9590.exe |
|Service Fabric for Windows Server |Service Fabric Standalone Installer Package | 9.1.1851.9590 |N/A | https://download.microsoft.com/download/8/3/6/836E3E99-A300-4714-8278-96BC3E8B5528/9.1.1851.9590/Microsoft.Azure.ServiceFabric.WindowsServer.9.1.1851.9590.zip |
||Service Fabric Standalone Runtime | 9.1.1851.9590 |N/A | https://download.microsoft.com/download/B/0/B/B0BCCAC5-65AA-4BE3-AB13-D5FF5890F4B5/9.1.1851.9590/MicrosoftAzureServiceFabric.9.1.1851.9590.cab |
|.NET SDK |Windows .NET SDK | 6.1.1851 |N/A | https://download.microsoft.com/download/b/8/a/b8a2fb98-0ec1-41e5-be98-9d8b5abf7856/MicrosoftServiceFabricSDK.6.1.1851.msi |
||Microsoft.ServiceFabric | 9.1.1851 |N/A |https://www.nuget.org |
||Reliable Services and Reliable Actors<br>\-Microsoft.ServiceFabric.Services<br>\-Microsoft.ServiceFabric.Services.Remoting<br>\-Microsoft.ServiceFabric.Services.Wcf <br>\-Microsoft.ServiceFabric.Actors <br>\-Microsoft.ServiceFabric.Actors.Wcf | 6.1.1851 |https://github.com/Azure/service-fabric-services-and-actors-dotnet |https://www.nuget.org |
||ASP.NET Core Service Fabric integration<br>\-Microsoft.ServiceFabric.Services.AspNetCore.*| 6.1.1851 |https://github.com/Azure/service-fabric-aspnetcore |https://www.nuget.org |
||Data, Diagnostics and Fabric transport<br>\-Microsoft.ServiceFabric.Data <br>\-Microsoft.ServiceFabric.Data.Interfaces <br>\-Microsoft.ServiceFabric.Diagnostics.Internal <br>\-Microsoft.ServiceFabric.FabricTransport/Internal | 6.1.1851 |N/A| https://www.nuget.org |
||Microsoft.ServiceFabric.Data.Extensions | 9.1.1851 | N/A |https://www.nuget.org |
|Java SDK |Java SDK | 1.0.6 |N/A |https://mvnrepository.com/artifact/com.microsoft.servicefabric/sf-actors/1.0.6 |
|Eclipse |Service Fabric plug-in for Eclipse | 2.0.7 | N/A |N/A |
|Yeoman |Azure Service Fabric Java generator | 1.0.7 |https://github.com/Azure/generator-azuresfjava |N/A |
||Azure Service Fabric C# generator | 1.0.9 |https://github.com/Azure/generator-azuresfcsharp |N/A |
||Azure Service Fabric guest executables generator | 1.0.1 |https://github.com/Azure/generator-azuresfguest |N/A|
||Azure Service Fabric Container generators | 1.0.1 |https://github.com/Azure/generator-azuresfcontainer |N/A |
|CLI |Service Fabric CLI | 11.0.1 |https://github.com/Azure/service-fabric-cli |https://pypi.python.org/pypi/sfctl |
|PowerShell |AzureRM.ServiceFabric | 0.3.15 |https://github.com/Azure/azure-powershell/tree/preview/src/ResourceManager/ServiceFabric |N/A  |
