---
title: Skapa ett erbjudande för virtuella Azure-datorer på Azure Marketplace med din egen avbildning
description: Lär dig hur du publicerar ett erbjudande om virtuella datorer för att Azure Marketplace med din egen avbildning.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 47fe7b42b68ae42f74a74e5fc69c8d1041d3bf8d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727132"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Så här skapar du en virtuell dator med din egen avbildning

Den här artikeln beskriver hur du skapar och distribuerar en avbildning av en virtuell dator (VM) som tillhandahålls av användaren.

> [!NOTE]
> Innan du börjar med den här proceduren granskar du [de tekniska kraven](marketplace-virtual-machines.md#technical-requirements) för erbjudanden för virtuella Azure-datorer, inklusive krav för virtuell hårddisk (VHD).

Om du i stället vill använda en godkänd basavbildning följer du anvisningarna i [Skapa en VM-avbildning från en godkänd bas](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Konfigurera den virtuella datorn

I det här avsnittet beskrivs hur du kan ändra storlek på, uppdatera och generalisera en virtuell Azure-dator. De här stegen är nödvändiga för att förbereda den virtuella datorn så att den distribueras Azure Marketplace.

### <a name="size-the-vhds"></a>Ändra storlek på de virtuella hårddiskarna

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Installera de senaste uppdateringarna

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Utföra fler säkerhetskontroller

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Utföra anpassad konfiguration och schemalagda aktiviteter

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>Generalisera avbildningen

Alla bilder i Azure Marketplace måste kunna återanvändas på ett allmänt sätt. För att uppnå detta måste den virtuella hårddisken för operativsystemet generaliseras, en åtgärd som tar bort alla instansspecifika identifierare och programvarudrivrutiner från en virtuell dator.

## <a name="bring-your-image-into-azure"></a>Ta din avbildning till Azure

> [!NOTE]
> Azure-prenumerationen som innehåller SIG måste finnas under samma klientorganisation som utgivarkontot för att kunna publicera. Dessutom måste utgivarkontot minst ha deltagaråtkomst till prenumerationen som innehåller SIG.

Det finns tre sätt att föra in avbildningen i Azure:

1. Ladda upp den virtuella hårddisken till Shared Image Gallery (SIG).
1. Ladda upp den virtuella hårddisken till ett Azure Storage-konto.
1. Extrahera den virtuella hårddisken från en hanterad avbildning (om du använder tjänster för att skapa avbildningar).

I följande tre avsnitt beskrivs dessa alternativ.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Alternativ 1: Ladda upp den virtuella hårddisken som Shared Image Gallery

1. Ladda upp virtuella hårddiskar till lagringskontot.
2. På Azure Portal du efter **Distribuera en anpassad mall**.
3. Välj **Skapa en egen mall i redigeraren**.
4. Kopiera följande arm Azure Resource Manager mall (arm).

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. Klistra in mallen i redigeraren.

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Exempelkodskärm för virtuell dator.":::

1. Välj **Spara**.
1. Använd parametrarna i den här tabellen för att slutföra fälten på skärmen som följer.

| Parametrar | Description |
| --- | --- |
| sourceStorageAccountResourceId | Resurs-ID för källlagringskontot där blob-VHD finns.<br><br>Om du vill hämta resurs-ID:t  går du till ditt **lagringskonto på Azure Portal,** går **till** Egenskaper och kopierar **ResourceID-värdet.** |
| sourceBlobUri | Blob-URI för OS-diskens VHD-blob (måste finnas i det angivna lagringskontot).<br><br>Om du vill hämta blob-URL:en går du **till ditt lagringskonto på Azure Portal**, går till din **blob** och kopierar  **URL-värdet.** |
| sourceBlobDataDisk0Uri | Blob-URI för datadiskens VHD-blob (måste finnas på det angivna lagringskontot). Om du inte har en datadisk tar du bort den här parametern från mallen.<br><br>Hämta blob-URL:en genom  att gå till **ditt lagringskonto på Azure Portal,** gå till **din blob** och kopiera **URL-värdet.** |
| sourceBlobDataDisk1Uri | Blob-URI för ytterligare datadisk-VHD-blob (måste finnas på det angivna lagringskontot). Om du inte har någon ytterligare datadisk tar du bort den här parametern från mallen.<br><br>Hämta blob-URL:en genom  att gå till **ditt lagringskonto på Azure Portal,** gå till **din blob** och kopiera **URL-värdet.** |
| galleryName | Namnet på Shared Image Gallery |
| galleryImageDefinitionName | Namnet på bilddefinitionen |
| galleryImageVersionName | Namnet på den avbildningsversion som ska skapas i följande format: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Visar det anpassade distributionsfönstret.":::

8. Välj **Granska + skapa**. När verifieringen är klar väljer du **Skapa**.

> [!TIP]
> Utgivarkontot måste ha "ägaråtkomst" för att publicera SIG-avbildningen. Om det behövs följer du stegen nedan för att bevilja åtkomst:
>
> 1. Gå till Shared Image Gallery (SIG).
> 2. Välj **Åtkomstkontroll** (IAM) på den vänstra panelen.
> 3. Välj **Lägg till** och sedan Lägg till **rolltilldelning.**
> 4. För **Roll** väljer du **Ägare.**
> 5. För **Tilldela åtkomst till** väljer du **Användare, grupp eller tjänstens huvudnamn.**
> 6. Ange Azure-e-postadressen för den person som ska publicera avbildningen.
> 7. Välj **Spara**.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Fönstret Lägg till rolltilldelning visas.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Alternativ 2: Ladda upp den virtuella hårddisken till ett lagringskonto

Konfigurera och förbered den virtuella dator som ska laddas upp enligt beskrivningen i Förbereda en Virtuell Windows-hårddisk eller [VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md) för uppladdning till Azure eller Skapa och ladda upp [en virtuell Linux-hårddisk.](../virtual-machines/linux/create-upload-generic.md)

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Alternativ 3: Extrahera den virtuella hårddisken från hanterad avbildning (om du använder tjänster för att skapa avbildningar)

Om du använder en bildskapande tjänst som [Packer](https://www.packer.io/)kan du behöva extrahera den virtuella hårddisken från avbildningen. Det finns inget direkt sätt att göra detta på. Du måste skapa en virtuell dator och extrahera den virtuella hårddisken från den virtuella datordisken.

## <a name="create-the-vm-on-the-azure-portal"></a>Skapa den virtuella datorn på Azure Portal

Följ de här stegen för att skapa den virtuella bas-VM-avbildningen [på Azure Portal](https://ms.portal.azure.com/).

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/).
2. Välj **Virtuella datorer**.
3. Välj **+ Lägg till** för att öppna skärmen Skapa en **virtuell** dator.
4. Välj avbildningen i listrutan eller välj Bläddra bland alla offentliga och privata **avbildningar för** att söka efter eller bläddra bland alla tillgängliga avbildningar av virtuella datorer.
5. Om du vill skapa en virtuell gen **2-dator** går **du till fliken** Avancerat och väljer alternativet Gen **2.**

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Välj Gen 1 eller Gen 2.":::

6. Välj storleken på den virtuella dator som ska distribueras.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Välj en rekommenderad VM-storlek för den valda avbildningen.":::

7. Ange övrig information som krävs för att skapa den virtuella datorn.
8. Välj **Granska + skapa för** att granska dina val. När meddelandet **Valideringen har** godkänts visas väljer du **Skapa**.

Azure börjar etablera den virtuella dator som du angav. Spåra förloppet genom att **Virtual Machines** på den vänstra menyn. När den virtuella datorn har skapats ändras statusen till **Körs.**

## <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

Läs följande dokumentation för att ansluta till din virtuella [Windows-](../virtual-machines/windows/connect-logon.md) eller [Linux-dator.](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm)

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Nästa steg

- [Testa den virtuella datoravbildningen](azure-vm-image-test.md) så att den uppfyller Azure Marketplace publiceringskrav. Detta är valfritt.
- Om du inte vill testa VM-avbildningen loggar du in på [Partnercenter](https://partner.microsoft.com/) och publicerar SIG-avbildningen (alternativ #1).
- Om du har följt #2 eller #3 [genererar du SAS-URI:t](azure-vm-get-sas-uri.md).
- Om du har problem med att skapa en ny Azure-baserad virtuell hårddisk kan du gå till [Vanliga frågor och svar om virtuella datorer Azure Marketplace](azure-vm-create-faq.md).
