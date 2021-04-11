---
title: Migrera till Azure Cloud Services (utökad support) med PowerShell
description: Så här migrerar du från Azure Cloud Services (klassisk) till Azure Cloud Services (utökad support) med PowerShell
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: eea49a41e81e7e580becce815ff91aff6aa430d6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287007"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>Migrera till Azure Cloud Services (utökad support) med PowerShell

De här stegen visar hur du använder Azure PowerShell-kommandon för att migrera från [Cloud Services (klassisk)](../cloud-services/cloud-services-choose-me.md) till [Cloud Services (utökad support)](overview.md).

> [!IMPORTANT]
> Migrering från Cloud Services (klassisk) till Cloud Services (utökad support) med migreringsverktyget är för närvarande en offentlig för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="1-plan-for-migration"></a>1) planera för migrering
Planering är det viktigaste steget för att få en lyckad migrering. Läs [Översikt över Cloud Services (utökad support)](overview.md) och [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-plan.md) innan du påbörjar några migrerings steg. 

## <a name="2-install-the-latest-version-of-powershell"></a>2) installera den senaste versionen av PowerShell
Det finns två huvudsakliga alternativ för att installera Azure PowerShell: [PowerShell-galleriet](https://www.powershellgallery.com/profiles/azure-sdk/) eller [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). WebPI tar emot månatliga uppdateringar. PowerShell-galleriet tar emot uppdateringar regelbundet. Den här artikeln baseras på Azure PowerShell version 2.1.0.

Installations anvisningar finns i [så här installerar och konfigurerar du Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0&preserve-view=true).

## <a name="3-ensure-admin-permissions"></a>3) se till att administratörs behörighet
För att utföra den här migreringen måste du läggas till som en medadministratör för prenumerationen i [Azure Portal](https://portal.azure.com).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn **hubb** väljer du **prenumeration**. Om du inte ser det väljer du **alla tjänster**.
3. Hitta rätt prenumerations post och titta sedan i fältet **min roll** . För en-administratör ska värdet vara *konto administratör*.

Om du inte kan lägga till en medadministratör kan du kontakta en tjänst administratör eller delad administratör för prenumerationen för att få till gång till dig själv.

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4) registrera den klassiska providern och CloudService-funktionen
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

Registrera hos resurs leverantören för migrering med hjälp av följande kommando:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> Registreringen är en engångs åtgärd, men du måste göra det en gång innan du försöker migrera. Utan registrering visas följande fel meddelande:
>
> *BadRequest: prenumerationen har inte registrerats för migrering.*

Registrera CloudServices-funktionen för din prenumeration. Det kan ta flera minuter att slutföra registreringen. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Vänta fem minuter tills registreringen är klar. 

Kontrol lera status för det klassiska leverantörs godkännandet med hjälp av följande kommando:

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Kontrol lera status för registreringen med följande:  
```powershell
Get-AzProviderFeature -FeatureName CloudServices
```

Se till att RegistrationState är `Registered` för båda innan du fortsätter.

Innan du växlar till den klassiska distributions modellen ser du till att du har tillräckligt med Azure Resource Manager vCPU kvot i Azure-regionen för din aktuella distribution eller det virtuella nätverket. Du kan använda följande PowerShell-kommando för att kontrol lera det aktuella antalet virtuella processorer som du har i Azure Resource Manager. Mer information om vCPU kvoter finns i [gränser och Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

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


## <a name="5-migrate-your-cloud-services"></a>5) migrera Cloud Services 
* [Migrera en moln tjänst som inte är i ett virtuellt nätverk](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [Migrera en moln tjänst i ett virtuellt nätverk](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> Alla åtgärder som beskrivs här är idempotenta. Om du har problem med en funktion som inte stöds eller ett konfigurations fel rekommenderar vi att du gör om åtgärden för att förbereda, avbryta eller bekräfta. Plattformen försöker sedan utföra åtgärden igen.


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5,1) alternativ 1 – migrera en moln tjänst som inte är i ett virtuellt nätverk
Hämta listan över moln tjänster med hjälp av följande kommando. Välj sedan den moln tjänst som du vill migrera.

```powershell
Get-AzureService | ft Servicename
```

Hämta distributions namnet för moln tjänsten. I det här exemplet är tjänst namnet **min tjänst**. Ersätt exempel tjänst namnet med ditt eget tjänst namn.

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

Kontrol lera först att du kan migrera moln tjänsten med hjälp av följande kommandon:

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

Följande kommando visar eventuella varningar och fel som blockerar migreringen. Om verifieringen lyckas kan du gå vidare till förberedelse steget.

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5,1) alternativ 2 – migrera en moln tjänst i ett virtuellt nätverk

Migrera en moln tjänst i ett virtuellt nätverk genom att migrera det virtuella nätverket. Moln tjänsten migreras automatiskt med det virtuella nätverket.

> [!NOTE]
> Det virtuella nätverks namnet kan skilja sig från vad som visas i den nya portalen. Det nya Azure Portal visar namnet som `[vnet-name]` , men det faktiska virtuella nätverks namnet är av typen `Group [resource-group-name] [vnet-name]` . Innan du påbörjar migreringen ska du leta upp det faktiska virtuella nätverks namnet med hjälp av kommandot `Get-AzureVnetSite | Select -Property Name` eller Visa det i det gamla Azure Portal. 

I det här exemplet anges det virtuella nätverks namnet till **myVnet**. Ersätt namnet på det virtuella nätverket i exemplet med ditt eget.

```powershell
$vnetName = "myVnet"
```

Kontrol lera först att du kan migrera det virtuella nätverket med hjälp av följande kommando:

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Följande kommando visar eventuella varningar och fel som blockerar migreringen. Om verifieringen lyckas kan du fortsätta med följande förberedelse steg:

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Kontrol lera konfigurationen för den för beredda moln tjänsten genom att antingen använda Azure PowerShell eller Azure Portal. Om du inte är redo för migrering och du vill gå tillbaka till det gamla läget använder du följande kommando:

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Om den för beredda konfigurationen ser bra ut kan du flytta framåt och bekräfta resurserna med hjälp av följande kommando:

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>Nästa steg
Granska ändringar i avsnittet [efter migreringen](in-place-migration-overview.md#post-migration-changes) om du vill se ändringar i operativsystemfiler, automation och andra attribut för din nya Cloud Services (utökad support) distribution. 
