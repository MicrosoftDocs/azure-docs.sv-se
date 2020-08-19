---
title: Konfigurera ett delat avbildnings galleri i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du konfigurerar ett delat avbildnings galleri i Azure DevTest Labs, vilket ger användare åtkomst till bilder från en delad plats när du skapar labb resurser.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7ec08fa741c1b52d3dd1d1e2b4247d3689190020
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271047"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurera ett delat bildgalleri i Azure DevTest Labs
DevTest Labs stöder nu funktionen för [delad bild galleri](../virtual-machines/windows/shared-image-galleries.md) . Det gör det möjligt för labb användare att komma åt bilder från en delad plats när du skapar labb resurser. Det hjälper dig också att bygga struktur och organisation runt dina anpassade avbildningar av virtuella datorer. Funktionen för delad bild galleri stöder:

- Hanterad global replikering av avbildningar
- Versions hantering och gruppering av avbildningar för enklare hantering
- Gör dina avbildningar med hög tillgänglighet med ZRS-konton (Zone redundant Storage) i regioner som har stöd för tillgänglighets zoner. ZRS erbjuder bättre återhämtning mot zonindelade-problem.
- Dela över prenumerationer och till och med mellan klienter, med rollbaserad åtkomst kontroll (RBAC).

Mer information finns i [dokumentationen för Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md). 
 
Om du har ett stort antal hanterade avbildningar som du behöver underhålla och vill göra dem tillgängliga i hela företaget, kan du använda ett delat avbildnings galleri som en lagrings plats som gör det enkelt att uppdatera och dela dina avbildningar. Som labb ägare kan du koppla ett befintligt delat avbildnings galleri till labbet. När det här galleriet är bifogat kan Lab-användare skapa datorer från dessa senaste avbildningar. En stor fördel med den här funktionen är att DevTest Labs nu kan dra nytta av att dela bilder i labb, i olika prenumerationer och i olika regioner. 

> [!NOTE]
> Mer information om kostnader som är associerade med tjänsten för delad avbildning finns i [fakturering för delade avbildnings Galleri](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Överväganden
- Du kan bara koppla ett delat bild galleri till ett labb i taget. Om du vill koppla ett annat galleri måste du koppla från det befintliga ett och koppla ett annat. 
- DevTest Labs har för närvarande inte stöd för att ladda upp bilder till galleriet via labbet. 
- När du skapar en virtuell dator med hjälp av en delad avbildning av galleriet, använder DevTest Labs alltid den senaste publicerade versionen av den här avbildningen. Om en avbildning har flera versioner kan användaren dock välja att skapa en dator från en tidigare version genom att gå till fliken Avancerade inställningar när du skapar en virtuell dator.  
- Även om DevTest Labs automatiskt gör ett bästa försök att se till att delade avbildnings Gallerien replikerar avbildningar till den region där laboratoriet finns, är det inte alltid möjligt. För att undvika att användare har problem med att skapa virtuella datorer från de här avbildningarna, se till att avbildningarna redan har repliker ATS till Labbets region. "

## <a name="use-azure-portal"></a>Använda Azure-portalen
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **alla tjänster** i den vänstra navigerings menyn.
1. Välj **DevTest Labs** från listan.
1. I listan med labb väljer du ditt **labb**.
1. Välj **konfiguration och principer** i avsnittet **Inställningar** på den vänstra menyn.
1. Välj **delade avbildnings gallerier** under **Virtual Machine Bases** på den vänstra menyn.

    ![Menyn delade avbildnings gallerier](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Koppla ett befintligt delat avbildnings galleri till labbet genom att klicka på knappen **bifoga** och välja ditt galleri i list rutan.

    ![Anslut](./media/configure-shared-image-gallery/attach-options.png)
1. När du har kopplat bild galleriet väljer du det för att gå till det kopplade galleriet. Konfigurera galleriet för att **Aktivera eller inaktivera** delade avbildningar för att skapa virtuella datorer. Välj ett bild galleri i listan om du vill konfigurera det. 

    Som standard **låter alla avbildningar användas som virtuella dator baser** anges till **Ja**. Det innebär att alla avbildningar som är tillgängliga i den bifogade delade avbildnings galleriet blir tillgängliga för en labb användare när du skapar en ny virtuell labb dator. Om åtkomst till vissa avbildningar behöver begränsas, ändra **Tillåt att alla avbildningar används som virtuella dator baser** till **Nej**, och välj de avbildningar som du vill tillåta när du skapar virtuella datorer och välj sedan knappen **Spara** .

    :::image type="content" source="./media/configure-shared-image-gallery/enable-disable.png" alt-text="Aktivera eller inaktivera bilder":::

    > [!NOTE]
    > Både generaliserade och specialiserade avbildningar i det delade avbildnings galleriet stöds. 
1. Labb användare kan sedan skapa en virtuell dator med hjälp av de aktiverade avbildningarna genom att klicka på **+ Lägg till** och hitta avbildningen på sidan **Välj en bassida** .

    ![Labb användare](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Använd Azure Resource Manager mall

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Bifoga ett delat avbildnings galleri i labbet
Om du använder en Azure Resource Manager mall för att koppla ett delat avbildnings galleri till labbet, måste du lägga till det under avsnittet resurser i Resource Manager-mallen, som du ser i följande exempel:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Ett exempel på en fullständig Resource Manager-mall finns i exemplen för Resource Manager-mallar i vår offentliga GitHub-lagringsplats: [Konfigurera ett delat avbildnings Galleri när du skapar ett labb](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-rest-api"></a>Använda REST-API

### <a name="get-a-list-of-labs"></a>Hämta en lista över labb 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>Hämta listan över delade avbildnings gallerier som är associerade med ett labb

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>Skapa eller uppdatera delade avbildnings Galleri

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="list-images-in-a-shared-image-gallery"></a>Lista bilder i ett delat avbildnings Galleri

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>Nästa steg
Se följande artiklar om hur du skapar en virtuell dator med hjälp av en avbildning från den bifogade delade avbildnings galleriet: [skapa en virtuell dator med en delad avbildning från galleriet](add-vm-use-shared-image.md)
