---
title: Aktivera VM-tillägg från Azure Portal
description: Den här artikeln beskriver hur du distribuerar tillägg för virtuella datorer till Azure Arc-aktiverade servrar som körs i hybrid moln miljöer från Azure Portal.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 43bbcef28b77e7c7112880fdac1bbd4809791cef
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728964"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Aktivera Azure VM-tillägg från Azure Portal

Den här artikeln visar hur du distribuerar och avinstallerar virtuella Azure-tillägg, som stöds av Azure Arc-aktiverade servrar, till en Linux-eller Windows hybrid dator via Azure Portal.

> [!NOTE]
> Key Vault VM-tillägget (för hands version) stöder inte distribution från Azure Portal, bara med Azure CLI, Azure PowerShell eller med en Azure Resource Manager mall.

## <a name="enable-extensions-from-the-portal"></a>Aktivera tillägg från portalen

VM-tillägg kan tillämpas på en server hanterad dator via Azure Portal.

1. Gå till [Azure Portal](https://portal.azure.com)i webbläsaren.

2. I portalen bläddrar du till **servrar – Azure Arc** och väljer hybrid datorn i listan.

3. Välj **tillägg** och välj sedan **Lägg till**. Välj det tillägg du vill använda i listan över tillgängliga tillägg och följ anvisningarna i guiden. I det här exemplet ska vi distribuera Log Analytics VM-tillägget.

    ![Välj VM-tillägg för den valda datorn](./media/manage-vm-extensions/add-vm-extensions.png)

    I följande exempel visas installationen av Log Analytics VM-tillägget från Azure Portal:

    ![Installera Log Analytics VM-tillägg](./media/manage-vm-extensions/mma-extension-config.png)

    För att slutföra installationen måste du ange arbetsyte-ID och primär nyckel. Om du inte är bekant med hur du hittar den här informationen kan du läsa [Hämta arbetsyte-ID och nyckel](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

4. När du har bekräftat nödvändig information som har angetts väljer du **skapa**. En sammanfattning av distributionen visas och du kan granska distributionens status.

>[!NOTE]
>Även om flera tillägg kan grupperas tillsammans och bearbetas, installeras de seriellt. När den första tilläggs installationen har slutförts görs ett försök att installera nästa tillägg.

## <a name="list-extensions-installed"></a>List tillägg har installerats

Du kan hämta en lista över VM-tilläggen på din ARC-aktiverade Server från Azure Portal. Utför följande steg för att se dem.

1. Gå till [Azure Portal](https://portal.azure.com)i webbläsaren.

2. I portalen bläddrar du till **servrar – Azure Arc** och väljer hybrid datorn i listan.

3. Välj **tillägg** och listan över installerade tillägg returneras.

    ![Lista över VM-tillägg distribuerade till den valda datorn](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>Avinstallera tillägg

Du kan ta bort ett eller flera tillägg från en ARC-aktiverad server från Azure Portal. Utför följande steg för att ta bort ett tillägg.

1. Gå till [Azure Portal](https://portal.azure.com)i webbläsaren.

2. I portalen bläddrar du till **servrar – Azure Arc** och väljer hybrid datorn i listan.

3. Välj **tillägg** och välj sedan ett tillägg i listan över installerade tillägg.

4. Välj **Avinstallera** och när du uppmanas att bekräfta väljer du **Ja** för att fortsätta.

## <a name="next-steps"></a>Nästa steg

- Du kan distribuera, hantera och ta bort VM-tillägg med [Azure CLI](manage-vm-extensions-cli.md)-, [PowerShell](manage-vm-extensions-powershell.md)-eller [Azure Resource Manager-mallarna](manage-vm-extensions-template.md).

- Felsöknings information finns i [fel söknings guiden för VM-tillägg](troubleshoot-vm-extensions.md).