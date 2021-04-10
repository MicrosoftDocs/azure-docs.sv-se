---
title: Uppdatera Media Services v3 efter åtkomst nycklar för rullande lagring | Microsoft Docs
description: Den här artikeln innehåller information om hur du uppdaterar Media Services v3 efter de rullande lagrings åtkomst nycklarna.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 76ba1b848b033c5a26e81be18bef23a2b4715a7e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572482"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>Uppdatera Media Services v3 efter åtkomst nycklar för rullande lagring

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Du uppmanas att välja ett Azure Storage-konto när du skapar ett nytt Azure Media Services-konto (AMS).  Du kan lägga till fler än ett lagrings konto till ditt Media Services-konto. Den här artikeln visar hur du roterar lagrings nycklar. Den visar också hur du lägger till lagrings konton till ett medie konto.

För att slutföra åtgärderna som beskrivs i den här artikeln bör du använda [Azure Resource Manager API: er](/rest/api/media/operations/azure-media-services-rest-api-reference) och [PowerShell](/powershell/module/az.media).  Mer information finns i [Hantera Azure-resurser med PowerShell och Resource Manager](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>Generering av lagrings åtkomst nyckel

När ett nytt lagrings konto skapas genererar Azure 2 512-bitars lagrings åtkomst nycklar som används för att autentisera åtkomsten till ditt lagrings konto. För att hålla dina lagrings anslutningar säkrare, återskapa och rotera lagrings åtkomst nyckeln regelbundet. Två åtkomst nycklar (primära och sekundära) tillhandahåller för att du ska kunna underhålla anslutningar till lagrings kontot med en åtkomst nyckel när du återskapar den andra åtkomst nyckeln. Den här proceduren kallas även "regler för" rullande åtkomst ".

Media Services är beroende av en lagrings nyckel som har angetts för den. Mer specifikt är de positionerare som används för att strömma eller hämta dina till gångar beroende av den angivna lagrings åtkomst nyckeln. När ett AMS-konto skapas, tar det ett beroende på den primära lagrings åtkomst nyckeln som standard. Men som användare kan du uppdatera lagrings nyckeln som AMS har. Du måste låta Media Services veta vilken nyckel som ska användas genom att följa dessa steg:

>[!NOTE]
> Om du har flera lagrings konton kan du utföra den här proceduren med varje lagrings konto. Den ordning som du roterar lagrings nycklarna i är inte fast. Du kan rotera den sekundära nyckeln först och sedan primär nyckeln eller vice versa.
>
> Innan du utför stegen i ett produktions konto måste du testa dem på ett för produktions konto.
>

## <a name="steps-to-rotate-storage-keys"></a>Steg för att rotera lagrings nycklar
 
 1. Ändra lagrings kontots primära nyckel via PowerShell-cmdleten eller [Azure](https://portal.azure.com/) Portal.
 2. Anropa `Sync-AzMediaServiceStorageKeys` cmdleten med lämpliga parametrar för att tvinga medie kontot att hämta lagrings konto nycklar
 
    I följande exempel visas hur du synkroniserar nycklar till lagrings konton.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Vänta en timme eller så. Kontrol lera att de strömmande scenarierna fungerar.
 4. Ändra lagrings kontots sekundära nyckel via PowerShell-cmdleten eller Azure Portal.
 5. Anropa `Sync-AzMediaServiceStorageKeys` PowerShell med lämpliga parametrar för att tvinga medie kontot att hämta nya lagrings konto nycklar.
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

Följande artikel visar hur du lägger till lagrings konton till ditt AMS-konto: [bifoga flera lagrings konton till ett Media Services-konto](media-services-managing-multiple-storage-accounts.md).
