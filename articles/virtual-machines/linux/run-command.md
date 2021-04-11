---
title: Köra gränssnitts skript i en virtuell Linux-dator på Azure
description: I det här avsnittet beskrivs hur du kör skript i en virtuell Azure Linux-dator med hjälp av funktionen Kör kommando
services: automation
ms.service: virtual-machines
ms.collection: linux
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 73dd15a5eed3e27d9b72bc0357e35901c04ba7a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552940"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Kör Shell-skript i din virtuella Linux-dator med kommandot kör

Funktionen Run Command använder agenten för virtuella datorer (VM) till att köra gränssnittsskript i en virtuell Azure Linux-dator. Du kan använda de här skripten till allmän hantering av datorer eller program. De kan hjälpa dig att snabbt diagnostisera och åtgärda åtkomst till virtuella datorer och nätverks problem och återställa den virtuella datorn till ett lyckat tillstånd.

## <a name="benefits"></a>Fördelar

Du kan komma åt dina virtuella datorer på flera olika sätt. Kommandot kör kan köra skript på dina virtuella datorer via en fjärr anslutning med hjälp av VM-agenten. Du använder kommandot kör via Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)eller [Azure CLI](/cli/azure/vm/run-command#az_vm_run_command_invoke) för virtuella Linux-datorer.

Den här funktionen är användbar i alla scenarier där du vill köra ett skript i en virtuell dator. Det är ett av de enda sätt som du kan använda för att felsöka och reparera en virtuell dator som inte har RDP-eller SSH-porten öppen på grund av en felaktig konfiguration av nätverks-eller administrativa användare.

## <a name="restrictions"></a>Begränsningar

Följande begränsningar gäller när du använder Kör kommando:

* Utdata är begränsade till de senaste 4 096 byten.
* Minimi tiden att köra ett skript är ungefär 20 sekunder.
* Skript körs som standard som en upphöjd användare i Linux.
* Du kan köra ett skript i taget.
* Skript som efterfrågar information (interaktivt läge) stöds inte.
* Du kan inte avbryta ett skript som körs.
* Den längsta tid som ett skript kan köras är 90 minuter. Efter det kommer skriptet att ta slut.
* Utgående anslutningar från den virtuella datorn krävs för att returnera resultatet av skriptet.

> [!NOTE]
> För att funktionen ska fungera korrekt måste körnings kommandot ansluta (port 443) till offentliga Azure-IP-adresser. Om tillägget inte har åtkomst till dessa slut punkter kan skripten köras utan problem, men inga resultat returneras. Om du blockerar trafik på den virtuella datorn kan du använda [service märken](../../virtual-network/network-security-groups-overview.md#service-tags) för att tillåta trafik till offentliga Azure-IP-adresser med hjälp av `AzureCloud` taggen.

## <a name="available-commands"></a>Tillgängliga kommandon

Den här tabellen visar en lista över kommandon som är tillgängliga för virtuella Linux-datorer. Du kan använda kommandot **RunShellScript** för att köra anpassade skript som du vill ha. När du använder Azure CLI eller PowerShell för att köra ett kommando måste värdet som du anger för `--command-id` `-CommandId` parametern eller vara något av följande värden i listan. När du anger ett värde som inte är ett tillgängligt kommando visas följande fel meddelande:

```error
The entity was not found in this Azure location
```

|**Namn**|**Beskrivning**|
|---|---|
|**RunShellScript**|Kör ett Linux Shell-skript.|
|**ifconfig**| Hämtar konfigurationen för alla nätverks gränssnitt.|

## <a name="azure-cli"></a>Azure CLI

I följande exempel används kommandot [AZ VM Run-Command](/cli/azure/vm/run-command#az_vm_run_command_invoke) för att köra ett gränssnitts skript på en virtuell Azure Linux-dator.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "apt-get update && apt-get install -y nginx"
```

> [!NOTE]
> Ange ett användar konto för att köra kommandon som en annan användare `sudo -u` .

## <a name="azure-portal"></a>Azure Portal

Gå till en virtuell dator i [Azure Portal](https://portal.azure.com) och välj **Kör kommando** under **åtgärder**. Du ser en lista över tillgängliga kommandon som ska köras på den virtuella datorn.

![Lista med kommandon](./media/run-command/run-command-list.png)

Välj ett kommando som ska köras. Några av kommandona kan ha valfria eller obligatoriska indataparametrar. För dessa kommandon visas parametrarna som textfält så att du kan ange indatavärdena. Du kan visa skriptet som körs genom att expandera **visnings skript** för varje kommando. **RunShellScript** skiljer sig från de andra kommandona, eftersom det gör att du kan ange ett eget anpassat skript.

> [!NOTE]
> De inbyggda kommandona kan inte redige ras.

När du har valt kommandot väljer du **Kör** för att köra skriptet. När skriptet har slutförts returneras utdata och eventuella fel i fönstret utdata. Följande skärm bild visar ett exempel på utdata från att köra kommandot **ifconfig** .

![Kör utdata från kommando skript](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

I följande exempel används cmdleten [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) för att köra ett PowerShell-skript på en virtuell Azure-dator. Cmdleten förväntar sig att skriptet som refereras till i `-ScriptPath` parametern ska vara lokalt till den plats där cmdleten körs.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Begränsa åtkomst till Run Command

Det krävs behörighet för att Visa körnings kommandon eller Visa information om ett kommando `Microsoft.Compute/locations/runCommands/read` . Den inbyggda [läsar](../../role-based-access-control/built-in-roles.md#reader) rollen och högre nivåer har den här behörigheten.

Du måste ha behörighet för att köra ett kommando `Microsoft.Compute/virtualMachines/runCommand/action` . Rollen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) och högre nivåer har den här behörigheten.

Du kan använda en av de [inbyggda rollerna](../../role-based-access-control/built-in-roles.md) eller skapa en [anpassad roll](../../role-based-access-control/custom-roles.md) för att använda kör-kommandot.

## <a name="next-steps"></a>Nästa steg

Information om andra sätt att köra skript och kommandon via fjärr anslutning i den virtuella datorn finns i [köra skript i din virtuella Linux-dator](run-scripts-in-vm.md).
