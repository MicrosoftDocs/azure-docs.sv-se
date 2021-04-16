---
title: Aktivera VM-tillägg från Azure Portal
description: Den här artikeln beskriver hur du distribuerar tillägg för virtuella datorer till Azure Arc-aktiverade servrar som körs i hybridmolnmiljöer från Azure Portal.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: b5b4ff79d68ec9ff0cc61b9dbb7d3c5d7fe93598
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388286"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Aktivera Azure VM-tillägg från Azure Portal

Den här artikeln visar hur du distribuerar och avinstallerar Azure VM-tillägg som stöds av Azure Arc-aktiverade servrar till en Linux- eller Windows-hybriddator via Azure Portal.

> [!NOTE]
> Tillägget Key Vault virtuell dator (förhandsversion) stöder inte distribution från Azure Portal, utan endast med Azure CLI, Azure PowerShell eller med hjälp av en Azure Resource Manager mall.

> [!NOTE]
> Azure Arc-aktiverade servrar stöder inte distribution och hantering av VM-tillägg till virtuella Azure-datorer. För virtuella Azure-datorer kan du läsa följande [översiktsartikel om VM-tillägg.](../../virtual-machines/extensions/overview.md)

## <a name="enable-extensions-from-the-portal"></a>Aktivera tillägg från portalen

VM-tillägg kan tillämpas på Din Arc för server hanterade datorer via Azure Portal.

1. Från webbläsaren går du till [Azure Portal](https://portal.azure.com).

2. I portalen bläddrar du till **Servrar Azure Arc** och väljer din hybriddator i listan.

3. Välj **Tillägg** och välj sedan Lägg **till**. Välj det tillägg som du vill använda i listan över tillgängliga tillägg och följ anvisningarna i guiden. I det här exemplet distribuerar vi Log Analytics VM-tillägget.

    ![Välj VM-tillägg för den valda datorn](./media/manage-vm-extensions/add-vm-extensions.png)

    I följande exempel visas installationen av Log Analytics VM-tillägget från Azure Portal:

    ![Installera Log Analytics VM-tillägg](./media/manage-vm-extensions/mma-extension-config.png)

    För att slutföra installationen måste du ange arbetsytans ID och primärnyckel. Om du inte vet hur du hittar den här informationen kan du gå till [Skaffa arbetsyte-ID och nyckel](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

4. När du har bekräftat den information som krävs väljer du **Skapa**. En sammanfattning av distributionen visas och du kan granska statusen för distributionen.

>[!NOTE]
>Flera tillägg kan batchas tillsammans och bearbetas, men de installeras seriellt. När den första installationen av tillägget är klar görs ett försök att installera nästa tillägg.

## <a name="list-extensions-installed"></a>Lista över installerade tillägg

Du kan hämta en lista över VM-tilläggen på din Arc-aktiverade server från Azure Portal. Utför följande steg för att se dem.

1. Från webbläsaren går du till [Azure Portal](https://portal.azure.com).

2. I portalen bläddrar du till **Servrar – Azure Arc** och väljer din hybriddator i listan.

3. Välj **Tillägg** så returneras listan över installerade tillägg.

    ![Visa en lista över VM-tillägg som distribuerats till den valda datorn](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>Avinstallera tillägg

Du kan ta bort ett eller flera tillägg från en Arc-aktiverad server från Azure Portal. Utför följande steg för att ta bort ett tillägg.

1. Från webbläsaren går du till [Azure Portal](https://portal.azure.com).

2. I portalen bläddrar du till **Servrar – Azure Arc** och väljer din hybriddator i listan.

3. Välj **Tillägg** och välj sedan ett tillägg i listan över installerade tillägg.

4. Välj **Avinstallera** och fortsätt genom att välja Ja när du **uppmanas** att verifiera.

## <a name="next-steps"></a>Nästa steg

- Du kan distribuera, hantera och ta bort VM-tillägg med [hjälp av Azure CLI,](manage-vm-extensions-cli.md) [PowerShell](manage-vm-extensions-powershell.md) [eller Azure Resource Manager mallar](manage-vm-extensions-template.md).

- Felsökningsinformation finns i felsökningsguiden [för VM-tillägg.](troubleshoot-vm-extensions.md)