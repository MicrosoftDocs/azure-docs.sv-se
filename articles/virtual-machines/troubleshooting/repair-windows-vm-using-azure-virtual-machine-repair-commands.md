---
title: Reparera en virtuell Windows-dator med hjälp av reparations kommandon för virtuell Azure-dator | Microsoft Docs
description: Den här artikeln beskriver hur du använder reparations kommandon för virtuella Azure-datorer för att ansluta disken till en annan virtuell Windows-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 7763c1d856a09acf2523a7073c3d300be6b70b37
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200710"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Reparera en virtuell Windows-dator med hjälp av reparationskommandon för virtuella Azure-datorer

Om din virtuella Windows-dator (VM) i Azure påträffar ett start-eller diskfel kan du behöva åtgärda problemet på själva disken. Ett vanligt exempel är en misslyckad program uppdatering som förhindrar att den virtuella datorn kan starta. Den här artikeln beskriver hur du använder reparations kommandon för virtuella Azure-datorer för att ansluta disken till en annan virtuell Windows-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn.

> [!IMPORTANT]
> * Skripten i den här artikeln gäller bara för de virtuella datorer som använder [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
> * Utgående anslutningar från den virtuella datorn (port 443) krävs för att skriptet ska kunna köras.
> * Det går bara att köra ett skript i taget.
> * Ett skript som körs kan inte avbrytas.
> * Den längsta tid som ett skript kan köras är 90 minuter, och det kommer att ta slut.
> * För virtuella datorer som använder Azure Disk Encryption stöds endast hanterade diskar som krypterats med enkel pass kryptering (med eller utan KEK).


## <a name="repair-process-overview"></a>Översikt över reparations processen

Nu kan du använda Azures reparations kommandon för virtuella datorer för att ändra OS-disk för en virtuell dator, och du behöver inte längre ta bort och återskapa den virtuella datorn.

Följ dessa steg för att felsöka problemet med den virtuella datorn:

1. Starta Azure Cloud Shell
2. Kör AZ-tillägget Lägg till/uppdatera.
3. Kör AZ VM Repair Create.
4. Kör AZ VM Repair Run eller utför åtgärder för minskning.
5. Kör AZ VM Repair Restore.

Mer dokumentation och instruktioner finns i [AZ VM Repair](/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Exempel på reparations processen

1. Starta Azure Cloud Shell

   Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den innehåller vanliga Azure-verktyg förinstallerade och konfigurerade för användning med ditt konto.

   Om du vill öppna Cloud Shell väljer du **testa den** i det övre högra hörnet i ett kodblock. Du kan också öppna Cloud Shell på en separat flik i webbläsaren genom att gå till [https://shell.azure.com](https://shell.azure.com) .

   Välj **Kopiera** för att kopiera kod blocken, klistra in koden i Cloud Shell och välj **RETUR** för att köra den.

   Om du föredrar att installera och använda detta CLI lokalt måste du köra Azure CLI version 2.0.30 eller senare. Kör ``az --version`` för att hitta versionen. Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).
   
   Om du behöver logga in på Cloud Shell med ett annat konto än vad du för närvarande är inloggad på Azure Portal med kan du använda ``az login`` [AZ-inloggnings referens](/cli/azure/reference-index#az-login&preserve-view=true).  Om du vill växla mellan prenumerationer som är kopplade till ditt konto kan du använda ``az account set --subscription`` [AZ konto uppsättnings referens](/cli/azure/account#az-account-set&preserve-view=true).

2. Om detta är första gången du har använt `az vm repair` kommandona, lägger du till tillägget VM-Repair cli.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Om du tidigare har använt `az vm repair` kommandona använder du eventuella uppdateringar i tillägget VM-Repair.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Kör `az vm repair create`. Det här kommandot skapar en kopia av OS-disken för den icke-funktionella virtuella datorn, skapar en virtuell reparations dator i en ny resurs grupp och kopplar disk kopian av operativ systemet.  Den virtuella reparations datorn kommer att ha samma storlek och region som den icke-funktionella virtuella datorn som angetts. Resurs gruppen och det virtuella dator namnet som används i alla steg är för den icke-funktionella virtuella datorn. Om den virtuella datorn använder Azure Disk Encryption försöker kommandot låsa upp den krypterade disken så att den är tillgänglig när den kopplas till den virtuella reparations datorn.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password 'password!234' --verbose
   ```

4. Kör `az vm repair run`. Det här kommandot kommer att köra det angivna reparations skriptet på den anslutna disken via den virtuella reparations datorn. Om fel söknings guiden som du använder har angett ett kör-ID ska du använda den här, annars kan du använda `az vm repair list-scripts` för att se tillgängliga reparations skript. Resurs gruppen och det virtuella dator namnet används här för den icke-funktionella virtuella datorn som användes i steg 3. Du hittar mer information om reparations skripten i [bibliotek för reparations skript](https://github.com/Azure/repair-script-library).

   ```azurecli-interactive
   az vm repair run -g MyResourceGroup -n MyVM --run-on-repair --run-id win-hello-world --verbose
   ```
   
   Du kan också utföra alla nödvändiga manuella åtgärder med hjälp av den virtuella reparations datorn och sedan gå vidare till steg 5.

5. Kör `az vm repair restore`. Det här kommandot byter ut den reparerade OS-disken mot den ursprungliga OS-disken för den virtuella datorn. Resurs gruppen och det virtuella dator namnet används här för den icke-funktionella virtuella datorn som användes i steg 3.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Verifiera och aktivera startdiagnostik

I följande exempel aktive ras Diagnostic-tillägget på den virtuella datorn som heter ``myVMDeployed`` i resurs gruppen med namnet ``myResourceGroup`` :

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Nästa steg

* Om du har problem med att ansluta till din virtuella dator kan du läsa [FELSÖKA RDP-anslutningar till en virtuell Azure-dator](./troubleshoot-rdp-connection.md).
* Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med program anslutningen på virtuella datorer i Azure](./troubleshoot-app-connection.md).
* Mer information om hur du använder Resource Manager finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).
