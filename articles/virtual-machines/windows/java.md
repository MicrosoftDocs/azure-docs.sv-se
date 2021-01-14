---
title: Skapa och hantera en virtuell Azure-dator med Java
description: Använd Java och Azure Resource Manager för att distribuera en virtuell dator och alla dess stöd resurser.
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/17/2017
ms.custom: devx-track-java
ms.author: cynthn
ms.openlocfilehash: 568760dcaebbad0e14405e14c5a5bf8f6cd18395
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197463"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Skapa och hantera virtuella Windows-datorer i Azure med Java

En [virtuell Azure-dator](overview.md) (VM) behöver flera stöd för Azure-resurser. Den här artikeln beskriver hur du skapar, hanterar och tar bort VM-resurser med Java. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Maven-projekt
> * Lägg till beroenden
> * Skapa autentiseringsuppgifter
> * Skapa resurser
> * Utföra hanterings uppgifter
> * Ta bort resurser
> * Kör programmet

Det tar ungefär 20 minuter att utföra dessa steg.

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

1. Installera [Java](/azure/developer/java/fundamentals/java-jdk-long-term-support)om du inte redan gjort det.
2. Installera [maven](https://maven.apache.org/download.cgi).
3. Skapa en ny mapp och projektet:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Lägg till beroenden

1. Under `testAzureApp` mappen öppnar du `pom.xml` filen och lägger till build-konfigurationen till &lt; projektet &gt; för att möjliggöra skapandet av ditt program:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Lägg till de beroenden som behövs för att få åtkomst till Azure Java SDK.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency>
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Spara filen.

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Innan du startar det här steget ska du kontrol lera att du har åtkomst till ett [Active Directory tjänstens huvud namn](../../active-directory/develop/howto-create-service-principal-portal.md). Du bör också registrera program-ID, autentiseringsnyckel och klient-ID som du behöver i ett senare steg.

### <a name="create-the-authorization-file"></a>Skapa verifierings filen

1. Skapa en fil med namnet `azureauth.properties` och Lägg till följande egenskaper:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Ersätt **&lt; prenumerations- &gt; ID** med prenumerations-ID, **&lt; program &gt; -ID** med Active Directory-program-ID, **&lt; autentisering- &gt; nyckel** med program nyckeln och klient-ID med klient- **&lt; ID: t &gt;** .

2. Spara filen.
3. Ange en miljö variabel med namnet AZURE_AUTH_LOCATION i ditt gränssnitt med den fullständiga sökvägen till autentiserings filen.

### <a name="create-the-management-client"></a>Skapa hanterings klienten

1. Öppna `App.java` filen under `src\main\java\com\fabrikam` och kontrol lera att paket utdraget är överst:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. Lägg till följande import uttryck under paket instruktionen:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. Om du vill skapa de Active Directory autentiseringsuppgifter som du behöver för att göra förfrågningar lägger du till den här koden i huvud metoden i klassen app:
   
    ```java
    try {
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Skapa resurser

### <a name="create-the-resource-group"></a>Skapa resursgruppen

Alla resurser måste finnas i en [resurs grupp](../../azure-resource-manager/management/overview.md).

Om du vill ange värden för programmet och skapa resurs gruppen lägger du till den här koden i try-blocket i huvud metoden:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Skapa tillgänglighets uppsättningen

[Tillgänglighets uppsättningar](tutorial-availability-sets.md) gör det enklare för dig att underhålla de virtuella datorer som används av ditt program.

Om du vill skapa tillgänglighets uppsättningen lägger du till den här koden i try-blocket i huvud metoden:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Skapa den offentliga IP-adressen

En [offentlig IP-adress](../../virtual-network/public-ip-addresses.md) krävs för att kommunicera med den virtuella datorn.

Om du vill skapa den offentliga IP-adressen för den virtuella datorn lägger du till den här koden i try-blocket i huvud metoden:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

En virtuell dator måste finnas i ett undernät för ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

Om du vill skapa ett undernät och ett virtuellt nätverk lägger du till den här koden i try-blocket i huvud metoden:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>Skapa nätverks gränssnittet

En virtuell dator behöver ett nätverks gränssnitt för att kommunicera med det virtuella nätverket.

Om du vill skapa ett nätverks gränssnitt lägger du till den här koden i try-blocket i huvud metoden:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

Nu när du har skapat alla stöd resurser kan du skapa en virtuell dator.

Lägg till den här koden till try-blocket i huvud metoden för att skapa den virtuella datorn:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> Den här självstudien skapar en virtuell dator som kör en version av operativ systemet Windows Server. Mer information om hur du väljer andra bilder finns i [navigera och välja avbildningar av virtuella Azure-datorer med Windows PowerShell och Azure CLI](../linux/cli-ps-findimage.md).
> 
>

Använd den här koden om du vill använda en befintlig disk i stället för en Marketplace-avbildning: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PremiumLRS)
    .create();

azure.virtualMachines.define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.StandardDS1)
    .create();
```

## <a name="perform-management-tasks"></a>Utföra hanterings uppgifter

Under livscykeln för en virtuell dator kan du vilja köra administrativa uppgifter, genom att till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa kod för att automatisera repetitiva eller komplexa uppgifter.

När du behöver göra något med den virtuella datorn måste du skaffa en instans av den. Lägg till den här koden till try-blocket för main-metoden:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Hämta information om den virtuella datorn

Om du vill hämta information om den virtuella datorn lägger du till den här koden i try-blocket i huvud metoden:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>Stoppa den virtuella datorn

Du kan stoppa en virtuell dator och behålla alla inställningar, men fortsätta att debiteras för den, eller så kan du stoppa en virtuell dator och frigöra den. När en virtuell dator frigörs frigörs även alla resurser som är kopplade till den och faktureringen upphör.

Om du vill stoppa den virtuella datorn utan att ta bort tilldelningen, lägger du till den här koden i try-blocket i huvud metoden:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Om du vill frigöra den virtuella datorn ändrar du avstängnings läge-anropet till den här koden:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Starta den virtuella datorn

För att starta den virtuella datorn lägger du till den här koden till try-blocket i huvud metoden:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Ändra storlek på den virtuella datorn

Många aspekter av distributionen bör övervägas när du bestämmer dig för en storlek på den virtuella datorn. Mer information finns i [VM-storlekar](../sizes.md).  

Om du vill ändra storleken på den virtuella datorn lägger du till den här koden i try-blocket i huvud metoden:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Lägga till en datadisk i en virtuell dator

Om du vill lägga till en datadisk till den virtuella datorn som är 2 GB i storlek, har ett LUN på 0, och en caching-typ av ReadWrite, lägger du till den här koden i try-blocket i huvud metoden:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Ta bort resurser

Eftersom du debiteras för resurser som används i Azure är det alltid en bra idé att ta bort resurser som inte längre behövs. Om du vill ta bort de virtuella datorerna och alla stödda resurser måste du ta bort resurs gruppen.

1. Om du vill ta bort resurs gruppen lägger du till den här koden i try-blocket i huvud metoden:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Spara App.java-filen.

## <a name="run-the-application"></a>Kör programmet

Det bör ta ungefär fem minuter för konsol programmet att köras helt från början till slut.

1. Om du vill köra programmet använder du följande maven-kommando:

    ```
    mvn compile exec:java
    ```

2. Innan du trycker på **RETUR** för att börja ta bort resurser kan det ta några minuter innan du verifierar att resurserna har skapats i Azure Portal. Klicka på distributions status om du vill se information om distributionen.


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om att använda [Azure-biblioteken för Java](/java/azure/java-sdk-azure-overview).