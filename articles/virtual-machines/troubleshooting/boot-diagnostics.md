---
title: Starta diagnostik för virtuella datorer i Azure | Microsoft doc
description: Översikt över de två fel söknings funktionerna för virtuella datorer i Azure
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 236390529ddaf84416448138d7b5bdf31caf681b
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827503"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Så här använder du startdiagnostik för att felsöka virtuella datorer i Azure

Det kan finnas många orsaker till att en virtuell dator övergår till ett icke startbart tillstånd. För att lösa problem med dina virtuella datorer som skapats med distributions modellen Resource Manager kan du använda följande fel söknings funktioner: konsol utdata och skärm bilds stöd för Azure Virtual Machines. 

För virtuella Linux-datorer kan du visa utdata från konsol loggen från portalen. För både virtuella Windows-och Linux-datorer kan du använda Azure för att visa en skärm bild av den virtuella datorn från hypervisorn. Båda funktionerna stöds för virtuella Azure-datorer i alla regioner. Observera att skärm bilder och utdata kan ta upp till 10 minuter innan de visas i ditt lagrings konto.

Du kan välja alternativet **Starta diagnostik** för att visa loggen och skärm bilden.

![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Vanliga startfel

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Inget operativsystem hittades](https://support.microsoft.com/help/4010142)
- [Startfel eller INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Aktivera diagnostik på en virtuell dator som skapats med Azure Portal

Följande procedur gäller för en virtuell dator som skapats med Resource Manager-distributions modellen.

På fliken **hantering** i avsnittet **övervakning** kontrollerar du att **startdiagnostik** är aktiverat. I list rutan **diagnostik-lagrings konto** väljer du ett lagrings konto där du vill placera diagnostikfiler.
 
![Skapa en virtuell dator](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> Funktionen för startdiagnostik stöder inte Premium Storage-kontot eller zon typerna redundanta lagrings konton. Om du använder Premium Storage-kontot för startdiagnostik kan du få ett StorageAccountTypeNotSupported-fel när du startar den virtuella datorn.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Distribuera från en Azure Resource Manager-mall

Om du distribuerar från en Azure Resource Manager-mall navigerar du till den virtuella dator resursen och lägger till diagnostisk profil avsnittet. Ange API-versions rubriken till "2015-06-15" eller senare. Den senaste versionen är "2018-10-01".

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

Via diagnostikprofilen kan du välja det lagringskonto där loggarna ska placeras.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Mer information om hur du distribuerar resurser med hjälp av mallar finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Aktivera startdiagnostik på den befintliga virtuella datorn 

Följ dessa steg om du vill aktivera startdiagnostik på en befintlig virtuell dator:

1. Logga in på [Azure Portal](https://portal.azure.com)och välj den virtuella datorn.
2. I avsnittet **support och fel sökning** väljer du **startdiagnostik** och väljer sedan fliken **Inställningar** .
3. I inställningar för **startdiagnostik** ändrar du status till **på** och i list rutan **lagrings konto** väljer du ett lagrings konto. 
4. Spara ändringen.

    ![Uppdatera befintlig virtuell dator](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Aktivera startdiagnostik med Azure CLI

Du kan använda Azure CLI för att aktivera startdiagnostik på en befintlig virtuell Azure-dator. Mer information finns i [AZ VM Boot-Diagnostics](/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
