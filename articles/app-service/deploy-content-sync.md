---
title: Synkronisera innehåll från en molnbaserad mapp
description: Lär dig hur du distribuerar din app för att Azure App Service via synkronisering av innehåll från en molnbaserad mapp, inklusive OneDrive eller Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051247"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synkronisera innehåll från en molnbaserad mapp till Azure App Service
Den här artikeln visar hur du synkroniserar ditt innehåll till [Azure App Service](./overview.md) från Dropbox och OneDrive. 

Med metoden för synkronisering av innehåll kan du arbeta med din app-kod och innehåll i en angiven distributionsmapp för att kontrol lera att den är i ett läge som är redo att distribuera och sedan synkronisera till App Service med en knapp tryckning. 

På grund av underliggande skillnader i API: erna stöds inte **OneDrive för företag för** tillfället.

> [!NOTE]
> **Utvecklings Center sidan (klassisk)** i Azure Portal, vilket är den gamla distributions miljön, kommer att bli inaktuell i mars 2021. Den här ändringen påverkar inte några befintliga distributions inställningar i appen och du kan fortsätta att hantera distribution av appar på sidan **distributions Center** .

## <a name="enable-content-sync-deployment"></a>Aktivera distribution av innehålls synkronisering

1. Gå till hanterings sidan för din App Service-app i [Azure Portal](https://portal.azure.com).

1. I den vänstra menyn klickar du på Inställningar för **distributions Center**  >  . 

1. I **källa** väljer du **OneDrive** eller **Dropbox**.

1. Klicka på **auktorisera** och följ instruktionerna för auktorisering. 

    ![Visar hur du godkänner OneDrive eller Dropbox i distributions centret i Azure Portal.](media/app-service-deploy-content-sync/choose-source.png)

    Du behöver bara auktorisera med OneDrive eller Dropbox en gång för ditt Azure-konto. Om du vill auktorisera ett annat OneDrive-eller Dropbox-konto för en app klickar du på **Ändra konto**.

1. I **mapp** väljer du den mapp som ska synkroniseras. Den här mappen skapas under följande angivna innehålls Sök väg i OneDrive eller Dropbox. 
   
    * **OneDrive**: `Apps\Azure Web Apps`
    * **Dropbox**: `Apps\Azure`
    
1. Klicka på **Spara**.

## <a name="synchronize-content"></a>Synkronisera innehåll

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. Gå till hanterings sidan för din App Service-app i [Azure Portal](https://portal.azure.com).

1. Klicka på **distributions Center**  >  **omdistribuera/synkronisera** på den vänstra menyn. 

    ![Visar hur du synkroniserar din Cloud-mapp med App Service.](media/app-service-deploy-content-sync/synchronize.png)
   
1. Bekräfta synkroniseringen genom att klicka på **OK** .

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Starta en synkronisering genom att köra följande kommando och Ersätt \<group-name> och \<app-name> :

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Starta en synkronisering genom att köra följande kommando och Ersätt \<group-name> och \<app-name> :

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>Inaktivera distribution av innehålls synkronisering

1. Gå till hanterings sidan för din App Service-app i [Azure Portal](https://portal.azure.com).

1. På den vänstra menyn klickar du på **distributions Center**  >  **Inställningar**  >  **Koppla från**. 

    ![Visar hur du kopplar från din mapp för molnappar med App Service-appen i Azure Portal.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera från lokal git-lagrings platsen](deploy-local-git.md)