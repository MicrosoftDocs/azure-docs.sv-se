---
title: Skapa ett erbjudande för virtuella Azure-datorer på Azure Marketplace med din egen avbildning
description: Lär dig hur du publicerar ett erbjudande för virtuella datorer på Azure Marketplace med din egen avbildning.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 4711ea76af83594ec529cfda13a308fbe6646398
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200465"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Så här skapar du en virtuell dator med en egen avbildning

Den här artikeln beskriver hur du skapar och distribuerar en användardefinierad avbildning av en virtuell dator (VM).

> [!NOTE]
> Innan du påbörjar den här proceduren bör du gå igenom de [tekniska kraven](marketplace-virtual-machines.md#technical-requirements) för virtuella Azure-datorer, inklusive krav på virtuell hård disk (VHD).

Om du vill använda en godkänd bas avbildning i stället följer du anvisningarna i [skapa en avbildning av en virtuell dator från en godkänd bas](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Konfigurera den virtuella datorn

I det här avsnittet beskrivs hur du ändrar storlek, uppdaterar och generaliserar en virtuell Azure-dator. Dessa steg är nödvändiga för att förbereda din virtuella dator så att den distribueras på Azure Marketplace.

### <a name="size-the-vhds"></a>Ändra storlek på de virtuella hård diskarna

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Installera de senaste uppdateringarna

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Utför fler säkerhets kontroller

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Utför anpassad konfiguration och schemalagda aktiviteter

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>Generalisera avbildningen

Alla avbildningar på Azure Marketplace måste kunna återanvändas på ett generiskt sätt. För att uppnå detta måste den virtuella hård disken generaliseras, en åtgärd som tar bort alla instans-/regionsspecifika identifierare och program driv rutiner från en virtuell dator.

## <a name="bring-your-image-into-azure"></a>Ta med din avbildning i Azure

Det finns tre sätt att hämta din avbildning i Azure:

1. Ladda upp den virtuella hård disken till ett delat avbildnings Galleri (SIG).
1. Överför den virtuella hård disken till ett Azure Storage-konto.
1. Extrahera den virtuella hård disken från en hanterad avbildning (om du använder avbildnings Bygg tjänster).

I följande tre avsnitt beskrivs dessa alternativ.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Alternativ 1: Ladda upp den virtuella hård disken som delad avbildnings Galleri

1. Ladda upp VHD: er till lagrings kontot.
2. På Azure Portal söker du efter **distribuera en anpassad mall**.
3. Välj **Bygg en egen mall i redigeraren**.
4. Kopiera följande Azure Resource Manager-mall (ARM).

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

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Exempel på kod skärm för virtuell dator.":::

1. Välj **Spara**.
1. Använd parametrarna i den här tabellen för att fylla i fälten på skärmen som följer.

| Parametrar | Beskrivning |
| --- | --- |
| sourceStorageAccountResourceId | Resurs-ID för det käll lagrings konto som BLOB-VHD: n finns i.<br><br>Om du vill hämta resurs-ID: t går du till ditt **lagrings konto** på **Azure Portal**, går till **Egenskaper** och kopierar värdet **ResourceID** . |
| sourceBlobUri | BLOB-URI för OS-diskens VHD-BLOB (måste vara i det angivna lagrings kontot).<br><br>Hämta BLOB-URL: en genom att gå till ditt **lagrings konto** på **Azure Portal**, gå till din **BLOB** och kopiera **URL** -värdet. |
| sourceBlobDataDisk0Uri | BLOB-URI för data diskens VHD-BLOB (måste finnas i det angivna lagrings kontot). Ta bort den här parametern från mallen om du inte har någon data disk.<br><br>Hämta BLOB-URL: en genom att gå till ditt **lagrings konto** på **Azure Portal**, gå till din **BLOB** och kopiera **URL** -värdet. |
| sourceBlobDataDisk1Uri | BLOB-URI för ytterligare data diskens VHD-BLOB (måste finnas i det angivna lagrings kontot). Om du inte har ytterligare en datadisk tar du bort den här parametern från mallen.<br><br>Hämta BLOB-URL: en genom att gå till ditt **lagrings konto** på **Azure Portal**, gå till din **BLOB** och kopiera **URL** -värdet. |
| galleryName | Namn på Galleri för delad avbildning |
| galleryImageDefinitionName | Bild definitionens namn |
| galleryImageVersionName | Namn på den avbildnings version som ska skapas i det här formatet: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Visar det anpassade distributions fönstret.":::

8. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**.

> [!TIP]
> Utgivar kontot måste ha "ägar behörighet" för att publicera SIG-avbildningen. Om det behövs följer du stegen nedan för att bevilja åtkomst:
>
> 1. Gå till galleriet för delade avbildningar (SIG).
> 2. Välj **åtkomst kontroll** (IAM) på den vänstra panelen.
> 3. Välj **Lägg till** och **Lägg sedan till roll tilldelning**.
> 4. För **roll** väljer du **ägare**.
> 5. För **tilldela åtkomst till** väljer du **användare, grupp eller tjänstens huvud namn**.
> 6. Ange Azure-e-postadressen för den person som ska publicera avbildningen.
> 7. Välj **Spara**.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Fönstret Lägg till roll tilldelning visas.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Alternativ 2: överför den virtuella hård disken till ett lagrings konto

Konfigurera och Förbered den virtuella datorn så att den laddas upp enligt beskrivningen i [förbereda en Windows VHD eller VHDX för att ladda upp till Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) eller [skapa och ladda upp en Linux-VHD](../virtual-machines/linux/create-upload-generic.md).

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Alternativ 3: extrahera den virtuella hård disken från den hanterade avbildningen (om du använder avbildnings Bygg tjänster)

Om du använder en avbildnings Bygg tjänst som [Packer](https://www.packer.io/)kan du behöva extrahera den virtuella hård disken från avbildningen. Det finns inget direkt sätt att göra detta. Du måste skapa en virtuell dator och extrahera den virtuella hård disken från den virtuella dator disken.

## <a name="create-the-vm-on-the-azure-portal"></a>Skapa den virtuella datorn på Azure Portal

Följ de här stegen för att skapa den virtuella dator avbildningen på [Azure Portal](https://ms.portal.azure.com/).

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/).
2. Välj **Virtuella datorer**.
3. Välj **+ Lägg** till för att öppna skärmen **skapa en virtuell dator** .
4. Välj avbildningen i list rutan eller Välj **Bläddra bland alla offentliga och privata avbildningar** för att söka eller bläddra bland alla tillgängliga avbildningar av virtuella datorer.
5. Om du vill skapa en virtuell dator i **generation 2** går du till fliken **Avancerat** och väljer alternativet **gen 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Välj gen 1 eller gen 2.":::

6. Välj storleken på den virtuella dator som ska distribueras.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Välj en rekommenderad VM-storlek för den valda avbildningen.":::

7. Ange övrig information som krävs för att skapa den virtuella datorn.
8. Välj **Granska + skapa** för att granska dina val. När meddelandet **verifieringen lyckades** visas väljer du **skapa**.

Azure börjar etablering av den virtuella dator som du har angett. Spåra förloppet genom att välja fliken **Virtual Machines** på den vänstra menyn. När den har skapats ändras statusen för den virtuella datorn till att **köras**.

## <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

Se följande dokumentation för att ansluta till din virtuella [Windows](../virtual-machines/windows/connect-logon.md) -eller [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) -dator.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Nästa steg

- [Testa din VM-avbildning](azure-vm-image-test.md) för att säkerställa att den uppfyller publicerings kraven för Azure Marketplace. Detta är valfritt.
- Om du inte vill testa din VM-avbildning loggar du in på [partner Center](https://partner.microsoft.com/) och publicerar sig-avbildningen (alternativ #1).
- Om du har följt alternativet #2 eller #3 [genererar du SAS-URI: n](azure-vm-get-sas-uri.md).
- Om du har problem med att skapa din nya Azure-baserade virtuella hård disk läser du [vanliga frågor och svar om virtuella datorer för Azure Marketplace](azure-vm-create-faq.md).
