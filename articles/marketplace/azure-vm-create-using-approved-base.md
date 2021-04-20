---
title: Skapa ett erbjudande för virtuella Azure-datorer (VM) från en godkänd bas, Azure Marketplace
description: Lär dig hur du skapar ett erbjudande för virtuella datorer från en godkänd bas.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 19ae4b929964aaeb971bef75a2a620e40e4667f5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727176"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Så här skapar du en virtuell dator med hjälp av en godkänd bas

Den här artikeln beskriver hur du använder Azure för att skapa en virtuell dator (VM) som innehåller ett förkonfigurerat, godkänt operativsystem. Om detta inte är kompatibelt med din lösning kan du skapa och konfigurera en lokal [virtuell dator](azure-vm-create-using-own-image.md) med hjälp av ett godkänt operativsystem.

> [!NOTE]
> Innan du börjar med den här proceduren granskar du [de tekniska kraven](marketplace-virtual-machines.md#technical-requirements) för erbjudanden för virtuella Azure-datorer, inklusive krav på virtuell hårddisk (VHD).

## <a name="select-an-approved-base-image"></a>Välj en godkänd basavbildning

Välj en av följande Windows- eller Linux-avbildningar som bas.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure erbjuder en mängd godkända Linux-distributioner. En aktuell lista finns i Linux på [distributioner som godkänts av Azure](../virtual-machines/linux/endorsed-distros.md).

## <a name="create-vm-on-the-azure-portal"></a>Skapa en virtuell dator på Azure Portal

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/).
2. Välj **Virtuella datorer**.
3. Välj **+ Lägg till** för att öppna skärmen Skapa en **virtuell** dator.
4. Välj avbildningen i listrutan eller välj Bläddra bland alla offentliga och privata **avbildningar för** att söka efter eller bläddra bland alla tillgängliga avbildningar av virtuella datorer.
5. Om du vill skapa en virtuell gen **2-dator** går **du till fliken** Avancerat och väljer alternativet Gen **2.**

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Välj Gen 1 eller Gen 2.":::

6. Välj storleken på den virtuella dator som ska distribueras.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Välj en rekommenderad VM-storlek för den valda avbildningen.":::

7. Ange övrig information som krävs för att skapa den virtuella datorn.
8. Välj **Granska + skapa för** att granska dina val. När meddelandet **Valideringen** har godkänts visas väljer du **Skapa.**

Azure börjar etablera den virtuella dator som du har angett. Spåra förloppet genom att **Virtual Machines** på den vänstra menyn. När den har skapats ändras statusen för Den virtuella datorn till **Körs.**

## <a name="configure-the-vm"></a>Konfigurera den virtuella datorn

I det här avsnittet beskrivs hur du kan ändra storlek på, uppdatera och generalisera en virtuell Azure-dator. De här stegen är nödvändiga för att förbereda den virtuella datorn så att den distribueras Azure Marketplace.

### <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

Läs följande dokumentation för att ansluta till din virtuella [Windows-](../virtual-machines/windows/connect-logon.md) eller [Linux-dator.](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm)

### <a name="install-the-most-current-updates"></a>Installera de senaste uppdateringarna

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Utföra ytterligare säkerhetskontroller

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Utföra anpassad konfiguration och schemalagda aktiviteter

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Nästa steg

- Rekommenderat nästa steg: [Testa den virtuella datoravbildningen](azure-vm-image-test.md) så att den uppfyller Azure Marketplace publiceringskrav. Detta är valfritt.
- Om du inte vill testa VM-avbildningen loggar du in på [Partnercenter för](https://partner.microsoft.com/) att publicera avbildningen.
- Om du har problem med att skapa en ny Azure-baserad virtuell hårddisk kan du gå till [Vanliga frågor och svar om virtuella datorer Azure Marketplace](azure-vm-create-faq.md).
