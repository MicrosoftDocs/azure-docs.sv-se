---
title: Återställa borttagna appar
description: Lär dig hur du återställer en borttagna app i Azure App Service. Undvik att råka ta bort en app av misstag.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e894e0a8bd20d6a1c3c833a4c0a3656c0dcd0f05
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833174"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Återställa borttagen App Service-app med hjälp av PowerShell

Om du råkar ta bort din app av misstag Azure App Service kan du återställa den med hjälp av kommandona från [Az PowerShell-modulen](/powershell/azure/).

> [!NOTE]
> - Borttagna appar tas bort från systemet 30 dagar efter den första borttagningen. När en app har rensats kan den inte återställas.
> - Funktionen för att ta inte tillbaka stöds inte för förbrukningsplanen.
> - Apps Service-appar som körs App Service-miljön en tjänst stöder inte ögonblicksbilder. Därför stöds inte funktionen för att ta bort och klona för App Service som körs i en App Service-miljön.
>

## <a name="re-register-app-service-resource-provider"></a>Registrera om App Service resursprovider

Vissa kunder kan stöta på ett problem där det inte går att hämta listan över borttagna appar. Lös problemet genom att köra följande kommando:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Lista borttagna appar

Om du vill hämta samlingen med borttagna appar kan du använda `Get-AzDeletedWebApp` .

Mer information om en specifik borttagna app som du kan använda:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Den detaljerade informationen omfattar:

- **DeletedSiteId:** Unik identifierare för appen som används för scenarier där flera appar med samma namn har tagits bort
- **SubscriptionID:** Prenumeration som innehåller den borttagna resursen
- **Plats:** Platsen för den ursprungliga appen
- **ResourceGroupName:** Namnet på den ursprungliga resursgruppen
- **Namn:** Namnet på den ursprungliga appen.
- **Plats:** namnet på facket.
- **Borttagningstid:** När togs appen bort  

## <a name="restore-deleted-app"></a>Återställa borttagna appar

>[!NOTE]
> `Restore-AzDeletedWebApp` stöds inte för funktionsappar.

När den app som du vill återställa har identifierats kan du återställa den med hjälp av `Restore-AzDeletedWebApp` .

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Distributionsfack återställs inte som en del av din app. Om du behöver återställa en mellanlagringsplats använder du `-Slot <slot-name>`  flaggan .
>

Indata för kommandot är:

- **Målresursgrupp:** Målresursgrupp där appen ska återställas
- **Namn:** Namnet på appen ska vara globalt unikt.
- **TargetAppServicePlanName:** App Service plan länkad till appen

Som standard `Restore-AzDeletedWebApp` återställs både din appkonfiguration och allt innehåll. Om du bara vill återställa innehåll använder du flaggan `-RestoreContentOnly` med den här kommandoleten.

> [!NOTE]
> Om appen finns på och sedan tas bort från en App Service-miljön kan den bara återställas om motsvarande App Service-miljön fortfarande finns.
>

Du hittar den fullständiga kommandoreferensen här: [Restore-AzDeletedWebApp](/powershell/module/az.websites/restore-azdeletedwebapp).
