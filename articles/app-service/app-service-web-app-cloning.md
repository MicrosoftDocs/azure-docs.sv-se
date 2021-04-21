---
title: Klona app med PowerShell
description: Lär dig hur du klonar App Service till en ny app med hjälp av PowerShell. En mängd olika kloningsscenarier beskrivs, inklusive Traffic Manager integrering.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 63ab20b16ae41aa48822f1b5c8e733c93d97f581
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833192"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure App Service appkloning med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Med versionen Microsoft Azure PowerShell version 1.1.0 har ett nytt alternativ lagts till som gör att du kan klona en befintlig App Service-app till en nyligen skapad app i en annan region eller i `New-AzWebApp` samma region. Med det här alternativet kan kunder snabbt och enkelt distribuera ett antal appar i olika regioner.

Appkloning stöds för App Service-planerna Standard, Premium, Premium V2 och Isolerad. Den nya funktionen använder samma begränsningar som App Service Backup finns i [Säkerhetskopiera en app i Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Klona en befintlig app
Scenario: En befintlig app i regionen USA, södra centrala och du vill klona innehållet till en ny app i regionen USA, norra centrala. Du kan göra det genom att använda Azure Resource Manager-versionen av PowerShell-cmdleten för att skapa en ny app med `-SourceWebApp` alternativet .

Om du känner till resursgruppens namn som innehåller källappen kan du använda följande PowerShell-kommando för att hämta källappens information (i det här fallet med namnet `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Om du vill skapa App Service plan kan du använda `New-AzAppServicePlan` kommandot som i följande exempel

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Med kommandot kan du skapa den nya appen i regionen USA, norra centrala och `New-AzWebApp` koppla den till en befintlig App Service plan. Dessutom kan du använda samma resursgrupp som källappen eller definiera en ny resursgrupp, som du ser i följande kommando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Om du vill klona en befintlig app, inklusive alla associerade distributionsfack, måste du använda `IncludeSourceWebAppSlots` parametern .  Observera att `IncludeSourceWebAppSlots` parametern endast stöds för kloning av en hel app, inklusive alla dess platser. Följande PowerShell-kommando visar hur du använder den parametern med `New-AzWebApp` kommandot :

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Om du vill klona en befintlig app inom samma region måste du skapa en ny resursgrupp och en ny App Service-plan i samma region och sedan använda följande PowerShell-kommando för att klona appen:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klona en befintlig app till en App Service-miljön
Scenario: En befintlig app i regionen USA, södra centrala och du vill klona innehållet till en ny app till en befintlig App Service-miljön (ASE).

Om du känner till resursgruppens namn som innehåller källappen kan du använda följande PowerShell-kommando för att hämta källappens information (i det här fallet med namnet `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Om du känner till ASE-namnet och resursgruppens namn som ASE tillhör kan du skapa den nya appen i den befintliga ASE:en enligt följande kommando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

Parametern `Location` krävs på grund av en äldre orsak, men den ignoreras när du skapar appen i en ASE. 

## <a name="cloning-an-existing-app-slot"></a>Klona ett befintligt appfack
Scenario: Du vill klona ett befintligt distributionsfack för en app till antingen en ny app eller ett nytt fack. Den nya appen kan finnas i samma region som det ursprungliga appfacket eller i en annan region.

Om du känner till resursgruppens namn som innehåller källappen kan du använda följande PowerShell-kommando för att hämta källappens information (i det här fallet med namnet `source-appslot` ) som är kopplad till `source-app` :

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Följande kommando visar hur du skapar en klon av källappen till en ny app:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurera Traffic Manager vid kloning av en app
Att skapa appar i flera regioner och konfigurera Azure Traffic Manager att dirigera trafik till alla dessa appar är ett viktigt scenario för att säkerställa att kundernas appar har hög åtkomst. När du klonar en befintlig app kan du ansluta båda apparna till antingen en ny Traffic Manager-profil eller en befintlig. Endast Azure Resource Manager version Traffic Manager stöds.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Skapa en ny Traffic Manager vid kloning av en app
Scenario: Du vill klona en app till en annan region när du konfigurerar en Azure Resource Manager Traffic Manager-profil som innehåller båda apparna. Följande kommando visar hur du skapar en klon av källappen till en ny app när du konfigurerar en Traffic Manager profil:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Lägga till en ny klonad app i en befintlig Traffic Manager profil
Scenario: Du har redan en Azure Resource Manager Traffic Manager-profil och vill lägga till båda apparna som slutpunkter. För att göra det måste du först sätta ihop det befintliga Traffic Manager-profil-ID:t. Du behöver prenumerations-ID: t, resursgruppens namn och det befintliga Traffic Manager-profilnamnet.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

När du har trafikmanger-ID:t visar följande kommando att du skapar en klon av källappen till en ny app samtidigt som du lägger till dem i en Traffic Manager profil:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```
> [!NOTE]
> Om du får ett felmeddelande om att "SSL-valideringen på Traffic Manager-värdnamnet misslyckas" föreslår vi att du använder attributet -IgnoreCustomHostNames i PowerShell-cmdleten när du utför kloningen eller i annat fall använder portalen.

## <a name="current-restrictions"></a>Aktuella begränsningar
Här är de kända begränsningarna för appkloning:

* Inställningarna för automatisk skalning klonas inte
* Inställningar för säkerhetskopieringsschema klonas inte
* VNET-inställningarna klonas inte
* App Insights konfigureras inte automatiskt i målappen
* Inställningarna för enkel autentisering klonas inte
* Kudu-tillägget klonas inte
* TiP-regler klonas inte
* Databasinnehållet klonas inte
* Utgående IP-adresser ändras vid kloning till en annan skalningsenhet
* Inte tillgängligt för Linux-appar
* Hanterade identiteter klonas inte

### <a name="references"></a>Referenser
* [App Service kloning](app-service-web-app-cloning.md)
* [Back up an app in Azure App Service](manage-backup.md)
* [Azure Resource Manager stöd för Azure Traffic Manager förhandsversion](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introduktion till App Service-miljöer](environment/intro.md)
* [Använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

