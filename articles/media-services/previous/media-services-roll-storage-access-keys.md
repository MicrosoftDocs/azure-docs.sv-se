---
title: Uppdatera Media Services efter åtkomst nycklar för rullande lagring | Microsoft Docs
description: De här artiklarna ger vägledning om hur du uppdaterar Media Services efter de rullande lagrings åtkomst nycklarna.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 12732171f774e6ce010f722cde4a27bb298275b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007948"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Uppdatera Media Services efter återställning av lagringsåtkomstnycklar

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

När du skapar ett nytt Azure Media Services-konto (AMS) uppmanas du också att välja ett Azure Storage konto som används för att lagra medie innehåll. Du kan lägga till fler än ett lagrings konto till ditt Media Services-konto. Den här artikeln visar hur du roterar lagrings nycklar. Den visar också hur du lägger till lagrings konton till ett medie konto. 

Om du vill utföra de åtgärder som beskrivs i den här artikeln ska du använda [Azure Resource Manager API: er](/rest/api/media/operations/azure-media-services-rest-api-reference) och [PowerShell](/powershell/module/az.media).  Mer information finns i [Hantera Azure-resurser med PowerShell och Resource Manager](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

När ett nytt lagrings konto skapas genererar Azure 2 512-bitars lagrings åtkomst nycklar som används för att autentisera åtkomsten till ditt lagrings konto. För att hålla dina lagrings anslutningar säkrare, rekommenderar vi att du regelbundet återskapar och roterar lagrings åtkomst nyckeln. Två åtkomst nycklar (primära och sekundära) tillhandahålls för att du ska kunna upprätthålla anslutningar till lagrings kontot med en åtkomst nyckel när du återskapar den andra åtkomst nyckeln. Den här proceduren kallas även "regler för" rullande åtkomst ".

Media Services är beroende av en lagrings nyckel som har angetts för den. Mer specifikt är de positionerare som används för att strömma eller hämta dina till gångar beroende av den angivna lagrings åtkomst nyckeln. När ett AMS-konto skapas, tar det ett beroende på den primära lagrings åtkomst nyckeln som standard, men som en användare kan du uppdatera lagrings nyckeln som AMS har. Du måste se till att Media Services vet vilken nyckel som ska användas genom att följa stegen som beskrivs i den här artikeln.  

>[!NOTE]
> Om du har flera lagrings konton kan du utföra den här proceduren med varje lagrings konto. Den ordning som du roterar lagrings nycklarna i är inte fast. Du kan rotera den sekundära nyckeln först och sedan primär nyckeln eller vice versa.
>
> Innan du kör steg som beskrivs i den här artikeln på ett produktions konto måste du testa dem på ett för produktions konto.
>

## <a name="steps-to-rotate-storage-keys"></a>Steg för att rotera lagrings nycklar 
 
 1. Ändra lagrings kontots primära nyckel via PowerShell-cmdleten eller [Azure](https://portal.azure.com/) Portal.
 2. Anropa Sync-AzMediaServiceStorageKeys-cmdlet med lämpliga parametrar för att tvinga medie kontot att hämta lagrings konto nycklar
 
    I följande exempel visas hur du synkroniserar nycklar till lagrings konton.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Vänta en timme eller så. Kontrol lera att de strömmande scenarierna fungerar.
 4. Ändra sekundär nyckel för lagrings konto via PowerShell-cmdleten eller Azure Portal.
 5. Anropa Sync-AzMediaServiceStorageKeys PowerShell med lämpliga parametrar för att tvinga medie kontot att hämta nya lagrings konto nycklar. 
 6. Vänta en timme eller så. Kontrol lera att de strömmande scenarierna fungerar.
 
### <a name="a-powershell-cmdlet-example"></a>Ett exempel på en PowerShell-cmdlet 

Följande exempel visar hur du hämtar lagrings kontot och synkroniserar det med AMS-kontot.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```
 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Steg för att lägga till lagrings konton till ditt AMS-konto

Följande artikel visar hur du lägger till lagrings konton till ditt AMS-konto: [bifoga flera lagrings konton till ett Media Services-konto](./media-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Tack till
Vi vill bekräfta följande personer som bidragit till att skapa det här dokumentet: Cenk Dingiloglu, Milano Gada, Seva Titov.
