---
title: Lägg till en virtuell dator med hjälp av en delad avbildning i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till en virtuell dator (VM) med en avbildning från den bifogade delade avbildnings galleriet i Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9421a1e21be9446b0e59328bd9a3730b57655274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483864"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Lägg till en virtuell dator med hjälp av en avbildning från den bifogade delade avbildnings galleriet
Med Azure DevTest Labs kan du koppla ett delat avbildnings galleri till labbet och sedan använda bilder i galleriet som baser för de virtuella datorer som du skapar i labbet. Information om hur du ansluter ett delat avbildnings galleri till labbet finns i [Konfigurera delade avbildnings galleriet](configure-shared-image-gallery.md). Den här artikeln visar hur du lägger till en virtuell dator i labbet med hjälp av en avbildning från den bifogade delade avbildnings galleriet som en bas. 

## <a name="azure-portal"></a>Azure Portal
I det här avsnittet får du lära dig hur du använder Azure Portal för att lägga till en virtuell dator i labbet baserat på en avbildning från den bifogade delade avbildnings galleriet. Det här avsnittet innehåller inte detaljerade anvisningar för hur du skapar en virtuell dator med hjälp av Azure Portal. Mer information finns i [skapa en VM-Azure Portal](devtest-lab-add-vm.md). Det visar bara de steg där du väljer en bild i det bifogade delade avbildnings galleriet och väljer en version av den avbildning som du vill använda. 

När du lägger till en virtuell dator i labbet kan du välja en avbildning från den bifogade delade avbildnings galleriet som en bas avbildning: 

![Välj en delad avbildning för basen](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

På fliken **Avancerade inställningar** på **resurs sidan Skapa labb** kan du sedan välja den version av avbildningen som du vill använda som bas avbildning:

![Välj avbildnings version](./media/add-vm-use-shared-image/select-version-shared-image.png)

Du kan växla till att använda en annan version av avbildningen när den virtuella datorn har skapats. 

## <a name="resource-manager-template"></a>Resource Manager-mall
Om du använder en Azure Resource Manager-mall för att skapa en virtuell dator med hjälp av en avbildning av ett delat bild galleri anger du ett värde för **sharedImageId** i avsnittet **Egenskaper** . Se följande exempel: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Ett exempel på en fullständig Resource Manager-mall finns i [skapa en virtuell dator med hjälp av ett delat bild galleri bilds](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) exempel i vår GitHub-lagringsplats. 

## <a name="rest-api"></a>REST-API

1. Först måste du hämta ID för avbildningen i det delade avbildnings galleriet. Ett sätt är att lista alla avbildningar i det bifogade delade avbildnings galleriet med hjälp av följande GET-kommando. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Anropa metoden för att skicka på virtuella datorer genom att skicka ID: t för den delade avbildningen som du fick från föregående anrop till `properties.SharedImageId` .

## <a name="next-steps"></a>Nästa steg
Information om hur du ansluter ett delat avbildnings galleri till ett labb och konfigurerar det finns i [Konfigurera delat avbildnings Galleri](configure-shared-image-gallery.md).