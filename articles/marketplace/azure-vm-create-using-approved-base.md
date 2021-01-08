---
title: Skapa ett erbjudande för virtuell Azure-dator från en godkänd bas, Azure Marketplace
description: Lär dig hur du skapar ett erbjudande för virtuell dator (VM) från en godkänd bas.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 01/06/2021
ms.openlocfilehash: 9164c1e2542024a02bf4868658d0f29728f32c7b
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976868"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Så här skapar du en virtuell dator med en godkänd bas

Den här artikeln beskriver hur du använder Azure för att skapa en virtuell dator (VM) som innehåller ett förkonfigurerat, godkänt operativ system. Om detta inte är kompatibelt med din lösning, är det möjligt att [skapa och konfigurera en lokal virtuell dator](azure-vm-create-using-own-image.md) med ett godkänt operativ system och sedan konfigurera och förbereda den för uppladdning enligt beskrivningen i [förbereda en Windows-VHD eller VHDX att ladda upp till Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!NOTE]
> Innan du påbörjar den här proceduren bör du gå igenom de [tekniska kraven](marketplace-virtual-machines.md#technical-requirements) för virtuella Azure-datorer, inklusive krav på virtuell hård disk (VHD).

## <a name="select-an-approved-base-image"></a>Välj en godkänd bas avbildning

Välj en av följande Windows-eller Linux-avbildningar som bas.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure erbjuder ett antal godkända Linux-distributioner. En aktuell lista finns i [Linux on distributioner som har godkänts av Azure](../virtual-machines/linux/endorsed-distros.md).

## <a name="create-vm-on-the-azure-portal"></a>Skapa en virtuell dator på Azure Portal

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/).
2. Välj **Virtuella datorer**.
3. Välj **+ Lägg** till för att öppna skärmen **skapa en virtuell dator** .
4. Välj avbildningen i list rutan eller Välj **Bläddra bland alla offentliga och privata avbildningar** för att söka eller bläddra bland alla tillgängliga avbildningar av virtuella datorer.
5. Om du vill skapa en virtuell dator i **generation 2** går du till fliken **Avancerat** och väljer alternativet **gen 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Välj gen 1 eller gen 2.":::

6. Välj storleken på den virtuella dator som ska distribueras.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Välj en rekommenderad VM-storlek för den valda avbildningen.":::

7. Ange övrig information som krävs för att skapa den virtuella datorn.
8. Välj **Granska + skapa** för att granska dina val. När meddelandet **verifieringen lyckades** visas väljer du  **skapa**.

Azure börjar etablering av den virtuella dator som du har angett. Spåra förloppet genom att välja fliken **Virtual Machines** på den vänstra menyn. När den har skapats ändras statusen för den virtuella datorn till att **köras**.

## <a name="configure-the-vm"></a>Konfigurera den virtuella datorn

I det här avsnittet beskrivs hur du ändrar storlek, uppdaterar och generaliserar en virtuell Azure-dator. Dessa steg är nödvändiga för att förbereda din virtuella dator så att den distribueras på Azure Marketplace.

### <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

Se följande dokumentation för att ansluta till din virtuella [Windows](../virtual-machines/windows/connect-logon.md) -eller [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) -dator.

### <a name="install-the-most-current-updates"></a>Installera de senaste uppdateringarna

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Utför ytterligare säkerhets kontroller

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Utför anpassad konfiguration och schemalagda aktiviteter

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Nästa steg

- Rekommenderat nästa steg: [testa din VM-avbildning](azure-vm-image-test.md) för att säkerställa att den uppfyller publicerings kraven för Azure Marketplace. Detta är valfritt.
- Om du inte testar din VM-avbildning fortsätter du med [att generera SAS-URI: n](azure-vm-get-sas-uri.md).
- Om du har problem med att skapa din nya Azure-baserade virtuella hård disk läser du [vanliga frågor och svar om virtuella datorer för Azure Marketplace](azure-vm-create-faq.md).