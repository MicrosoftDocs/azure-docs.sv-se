---
title: Migrera till Resource Manager med PowerShell
description: Den här artikeln beskriver den plattforms oberoende migreringen av IaaS-resurser, till exempel virtuella datorer, virtuella nätverk och lagrings konton från klassisk till Azure Resource Manager med hjälp av Azure PowerShell kommandon
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: cf020dba240d87e2e7c1c291e3c15df9611395e8
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507519"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrera IaaS-resurser från klassisk till Azure Resource Manager med hjälp av PowerShell

> [!IMPORTANT]
> Idag, cirka 90% av virtuella IaaS-datorer använder [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att dras tillbaka den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur den påverkar dig](../classic-vm-deprecation.md#how-does-this-affect-me).

De här stegen visar hur du använder Azure PowerShell-kommandon för att migrera infrastruktur som en tjänst (IaaS) resurser från den klassiska distributions modellen till Azure Resource Manager distributions modell.

Om du vill kan du även migrera resurser med hjälp av [Azure CLI](../linux/migration-classic-resource-manager-cli.md).

* Information om hur du migrerar scenarier som stöds finns i [plattforms stöd för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Detaljerad vägledning och genom gång av migrering finns i [teknisk djupgående om migrering av plattformar som stöds från klassisk till Azure Resource Manager](../migration-classic-resource-manager-deep-dive.md).
* [Granska de vanligaste migrerings felen](migration-classic-resource-manager-errors.md).

<br>
Här är ett flödes schema för att identifiera i vilken ordning stegen måste utföras under en migreringsprocessen.

![Skärmbild som visar migreringsstegen](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Steg 1: planera för migrering
Här följer några bra metoder som vi rekommenderar när du utvärderar om du vill migrera IaaS-resurser från klassisk till Resource Manager:

* Läs igenom de [funktioner och konfigurationer som stöds och inte](migration-classic-resource-manager-overview.md)stöds. Om du har virtuella datorer som använder konfigurationer eller funktioner som inte stöds, väntar du tills konfigurationen eller funktions stödet har meddelats. Om den passar dina behov kan du ta bort den funktionen eller flytta ut från den konfigurationen för att aktivera migrering.
* Om du har automatiserade skript som distribuerar din infrastruktur och dina program idag kan du försöka skapa en liknande test installation genom att använda dessa skript för migrering. Du kan också konfigurera exempel miljöer med hjälp av Azure Portal.

> [!IMPORTANT]
> Application gateways stöds för närvarande inte för migrering från klassisk till Resource Manager. Om du vill migrera ett virtuellt nätverk med en Application Gateway tar du bort gatewayen innan du kör en förberedelse åtgärd för att flytta nätverket. När du har slutfört migreringen ansluter du gatewayen i Azure Resource Manager.
>
> Azure ExpressRoute-gatewayer som ansluter till ExpressRoute-kretsar i en annan prenumeration kan inte migreras automatiskt. Ta i så fall bort ExpressRoute-gatewayen, migrera det virtuella nätverket och återskapa gatewayen. Mer information finns i [migrera ExpressRoute-kretsar och associerade virtuella nätverk från den klassiska distributions modellen till Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Steg 2: installera den senaste versionen av PowerShell
Det finns två huvudsakliga alternativ för att installera Azure PowerShell: [PowerShell-galleriet](https://www.powershellgallery.com/profiles/azure-sdk/) eller [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). WebPI tar emot månatliga uppdateringar. PowerShell-galleriet tar emot uppdateringar regelbundet. Den här artikeln baseras på Azure PowerShell version 2.1.0.

Installations anvisningar finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Steg 3: kontrol lera att du är administratör för prenumerationen
För att utföra den här migreringen måste du läggas till som en medadministratör för prenumerationen i [Azure Portal](https://portal.azure.com).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn **hubb** väljer du **prenumeration**. Om du inte ser det väljer du **alla tjänster**.
3. Hitta rätt prenumerations post och titta sedan i fältet **min roll** . För en-administratör ska värdet vara _konto administratör_.

Om du inte kan lägga till en-administratör kan du kontakta en tjänst administratör eller en samadministratör för prenumerationen för att få till gång till den.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Steg 4: Konfigurera din prenumeration och registrera dig för migrering
Starta först en PowerShell-prompt. För migrering konfigurerar du din miljö för både klassisk och Resource Manager.

Logga in på ditt konto för Resource Manager-modellen.

```powershell
    Connect-AzAccount
```

Hämta tillgängliga prenumerationer med hjälp av följande kommando:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Ange din Azure-prenumeration för den aktuella sessionen. I det här exemplet anges standard prenumerations namnet för **min Azure-prenumeration**. Ersätt exempel prenumerations namnet med ditt eget.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Registreringen är en engångs åtgärd, men du måste göra det en gång innan du försöker migrera. Utan registrering visas följande fel meddelande:
>
> *BadRequest: prenumerationen har inte registrerats för migrering.*

Registrera hos resurs leverantören för migrering med hjälp av följande kommando:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Vänta fem minuter tills registreringen är klar. Kontrol lera status för godkännandet med hjälp av följande kommando:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Kontrol lera att RegistrationState är `Registered` innan du fortsätter.

Innan du växlar till den klassiska distributions modellen ser du till att du har tillräckligt med Azure Resource Manager virtuella virtuella processorer i Azure-regionen för din aktuella distribution eller det virtuella nätverket. Du kan använda följande PowerShell-kommando för att kontrol lera det aktuella antalet virtuella processorer som du har i Azure Resource Manager. Mer information om vCPU kvoter finns i [gränser och Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

I det här exemplet kontrol leras tillgängligheten i regionen **USA, västra** . Ersätt exempel områdets namn med ditt eget.

```powershell
    Get-AzVMUsage -Location "West US"
```

Logga nu in på ditt konto för den klassiska distributions modellen.

```powershell
    Add-AzureAccount
```

Hämta tillgängliga prenumerationer med hjälp av följande kommando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Ange din Azure-prenumeration för den aktuella sessionen. I det här exemplet anges standard prenumerationen för **min Azure-prenumeration**. Ersätt exempel prenumerations namnet med ditt eget.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Steg 5: kör kommandon för att migrera dina IaaS-resurser
* [Migrera virtuella datorer i en moln tjänst (inte i ett virtuellt nätverk)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrera virtuella datorer i ett virtuellt nätverk](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrera ett lagrings konto](#step-52-migrate-a-storage-account)

> [!NOTE]
> Alla åtgärder som beskrivs här är idempotenta. Om du har problem med en funktion som inte stöds eller ett konfigurations fel rekommenderar vi att du gör om åtgärden för att förbereda, avbryta eller bekräfta. Plattformen försöker sedan utföra åtgärden igen.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Steg 5,1: alternativ 1 – migrera virtuella datorer i en moln tjänst (inte i ett virtuellt nätverk)
Hämta listan över moln tjänster med hjälp av följande kommando. Välj sedan den moln tjänst som du vill migrera. Om de virtuella datorerna i moln tjänsten finns i ett virtuellt nätverk eller om de har webb-eller arbets roller, returnerar kommandot ett fel meddelande.

```powershell
    Get-AzureService | ft Servicename
```

Hämta distributions namnet för moln tjänsten. I det här exemplet är tjänst namnet **min tjänst**. Ersätt exempel tjänst namnet med ditt eget tjänst namn.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Förbered de virtuella datorerna i moln tjänsten för migrering. Du kan välja mellan två alternativ.

* **Alternativ 1: migrera de virtuella datorerna till ett plattforms skapat virtuellt nätverk.**

    Kontrol lera först att du kan migrera moln tjänsten med hjälp av följande kommandon:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Följande kommando visar eventuella varningar och fel som blockerar migreringen. Om verifieringen lyckas kan du gå vidare till förberedelse steget.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Alternativ 2: migrera till ett befintligt virtuellt nätverk i distributions modellen för Resource Manager.**

    I det här exemplet anges resurs gruppens namn till **myResourceGroup**, det virtuella nätverks namnet till **myVirtualNetwork**och under nätets namn till **under nätet**. Ersätt namnen i exemplet med namnen på dina egna resurser.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Kontrol lera först att du kan migrera det virtuella nätverket med hjälp av följande kommando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Följande kommando visar eventuella varningar och fel som blockerar migreringen. Om verifieringen lyckas kan du fortsätta med följande förberedelse steg:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

När förberedelse åtgärden har slutförts med något av föregående alternativ kan du fråga efter migreringen för de virtuella datorerna. Se till att de är i samma `Prepared` tillstånd.

I det här exemplet anges namnet på den virtuella datorn till **myVM**. Ersätt exempel namnet med ditt eget VM-namn.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Kontrol lera konfigurationen för de för beredda resurserna genom att använda antingen PowerShell eller Azure Portal. Om du inte är redo för migrering och du vill gå tillbaka till det gamla läget använder du följande kommando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Om den för beredda konfigurationen ser bra ut kan du flytta framåt och bekräfta resurserna med hjälp av följande kommando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Steg 5,1: alternativ 2 – migrera virtuella datorer i ett virtuellt nätverk

Om du vill migrera virtuella datorer i ett virtuellt nätverk migrerar du det virtuella nätverket. De virtuella datorerna migreras automatiskt till det virtuella nätverket. Välj det virtuella nätverk som du vill migrera.
> [!NOTE]
> [Migrera en enskild virtuell dator](./create-vm-specialized-portal.md) som skapats med den klassiska distributions modellen genom att skapa en ny virtuell Resource Manager-dator med Managed disks med hjälp av VHD-filerna (OS och data) för den virtuella datorn.
<br>

> [!NOTE]
> Det virtuella nätverks namnet kan skilja sig från vad som visas i den nya portalen. Det nya Azure Portal visar namnet som `[vnet-name]` , men det faktiska virtuella nätverks namnet är av typen `Group [resource-group-name] [vnet-name]` . Innan du påbörjar migreringen ska du leta upp det faktiska virtuella nätverks namnet med hjälp av kommandot `Get-AzureVnetSite | Select -Property Name` eller Visa det i det gamla Azure Portal. 

I det här exemplet anges det virtuella nätverks namnet till **myVnet**. Ersätt namnet på det virtuella nätverket i exemplet med ditt eget.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Om det virtuella nätverket innehåller webb-eller arbets roller eller virtuella datorer med konfigurationer som inte stöds får du ett verifierings fel meddelande.

Kontrol lera först att du kan migrera det virtuella nätverket med hjälp av följande kommando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Följande kommando visar eventuella varningar och fel som blockerar migreringen. Om verifieringen lyckas kan du fortsätta med följande förberedelse steg:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Kontrol lera konfigurationen för de för beredda virtuella datorerna genom att använda antingen Azure PowerShell eller Azure Portal. Om du inte är redo för migrering och du vill gå tillbaka till det gamla läget använder du följande kommando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Om den för beredda konfigurationen ser bra ut kan du flytta framåt och bekräfta resurserna med hjälp av följande kommando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Steg 5,2: Migrera ett lagrings konto
När du har migrerat de virtuella datorerna utför du följande nödvändiga kontroller innan du migrerar lagrings kontona.

> [!NOTE]
> Om ditt lagrings konto inte har några associerade diskar eller VM-data kan du gå direkt till avsnittet Verifiera lagrings konton och starta migrering.

* Krav kontroller om du har migrerat några virtuella datorer eller om ditt lagrings konto har disk resurser:
    * Migrera virtuella datorer vars diskar lagras i lagrings kontot.

        Följande kommando returnerar RoleName-och DiskName-egenskaperna för alla VM-diskar i lagrings kontot. RoleName är namnet på den virtuella dator som en disk är ansluten till. Om det här kommandot returnerar diskar ska du se till att de virtuella datorer som dessa diskar är anslutna till migreras innan du migrerar lagrings kontot.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Ta bort ej anslutna virtuella dator diskar som lagras i lagrings kontot.

        Hitta ej anslutna virtuella dator diskar i lagrings kontot med hjälp av följande kommando:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Om föregående kommando returnerar diskar tar du bort diskarna genom att använda följande kommando:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Ta bort VM-avbildningar som lagras i lagrings kontot.

        Följande kommando returnerar alla VM-avbildningar med OS-diskar som lagras i lagrings kontot.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Följande kommando returnerar alla VM-avbildningar med data diskar som lagras i lagrings kontot.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Ta bort alla VM-avbildningar som returneras av föregående kommandon med hjälp av det här kommandot:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Verifiera lagrings konton och starta migreringen.

    Verifiera varje lagrings konto för migrering med hjälp av följande kommando. I det här exemplet är lagrings kontots namn **myStorageAccount**. Ersätt exempel namnet med namnet på ditt eget lagrings konto.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Nästa steg är att förbereda lagrings kontot för migrering.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Kontrol lera konfigurationen för det för beredda lagrings kontot genom att antingen använda Azure PowerShell eller Azure Portal. Om du inte är redo för migrering och du vill gå tillbaka till det gamla läget använder du följande kommando:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Om den för beredda konfigurationen ser bra ut kan du flytta framåt och bekräfta resurserna med hjälp av följande kommando:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Nästa steg
* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](../migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrering av IaaS-resurser från klassisk till Azure Resource Manager](../migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
