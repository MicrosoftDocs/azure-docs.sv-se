---
title: Köra PowerShell-skript på en virtuell Windows-dator i Azure
description: Det här avsnittet beskriver hur du kör PowerShell-skript i en virtuell Azure Windows-dator med hjälp av Kör kommando-funktionen
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: 3271f5461447439772b656b8927a54057c8b0c7e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786413"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Kör PowerShell-skript på din virtuella Windows-dator med hjälp av Kör kommando

Funktionen Kör kommando använder VM-agenten för att köra PowerShell-skript på en virtuell Azure Windows-dator. Du kan använda de här skripten till allmän hantering av datorer eller program. De kan hjälpa dig att snabbt diagnostisera och åtgärda problem med VM-åtkomst och nätverk och få tillbaka den virtuella datorn till ett bra tillstånd.



## <a name="benefits"></a>Fördelar

Du kan komma åt dina virtuella datorer på flera olika sätt. Kör kommando kan köra skript på dina virtuella datorer via fjärrstyrning med hjälp av VM-agenten. Du använder Kör kommando via Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)eller [PowerShell för](/powershell/module/az.compute/invoke-azvmruncommand) virtuella Windows-datorer.

Den här funktionen är användbar i alla scenarier där du vill köra ett skript på en virtuell dator. Det är ett av de enda sätten att felsöka och åtgärda en virtuell dator som inte har RDP- eller SSH-porten öppen på grund av felaktig nätverkskonfiguration eller administrativ användarkonfiguration.

## <a name="restrictions"></a>Begränsningar

Följande begränsningar gäller när du använder Kör kommando:

* Utdata är begränsade till de senaste 4 096 byte.
* Den minsta tiden för att köra ett skript är cirka 20 sekunder.
* Skript körs som System i Windows.
* Ett skript i taget kan köras.
* Skript som frågar efter information (interaktivt läge) stöds inte.
* Du kan inte avbryta ett skript som körs.
* Den maximala tid som ett skript kan köras är 90 minuter. Efter det tar det slut.
* Utgående anslutning från den virtuella datorn krävs för att returnera resultatet av skriptet.
* Vi rekommenderar inte att du kör ett skript som orsakar ett stopp eller en uppdatering av VM-agenten. Detta kan låta tillägget vara i övergångstillstånd, vilket leder till en tidsgräns.

> [!NOTE]
> För att fungera korrekt Kör kommando anslutning (port 443) till offentliga IP-adresser i Azure. Om tillägget inte har åtkomst till dessa slutpunkter kan skripten köras utan att returnera resultatet. Om du blockerar trafik på den virtuella [](../../virtual-network/network-security-groups-overview.md#service-tags) datorn kan du använda tjänsttaggar för att tillåta trafik till offentliga Azure-IP-adresser med hjälp av `AzureCloud` taggen .

## <a name="available-commands"></a>Tillgängliga kommandon

Den här tabellen visar en lista över kommandon som är tillgängliga för virtuella Windows-datorer. Du kan använda kommandot **RunPowerShellScript** för att köra alla anpassade skript som du vill. När du använder Azure CLI eller PowerShell för att köra ett kommando måste värdet som du anger för parametern eller vara något av `--command-id` `-CommandId` följande värden i listan. När du anger ett värde som inte är ett tillgängligt kommando visas det här felet:

```error
The entity was not found in this Azure location
```

|**Namn**|**Beskrivning**|
|---|---|
|**RunPowerShellScript**|Kör ett PowerShell-skript.|
|**EnableRemotePS**|Konfigurerar datorn för att aktivera fjärr-PowerShell.|
|**EnableAdminAccount**|Kontrollerar om det lokala administratörskontot är inaktiverat och aktiverar det i så fall.|
|**Ipconfig**| Visar detaljerad information om IP-adressen, nätmasken och standardgatewayen för varje kort som är bundet till TCP/IP.|
|**RDPSettings**|Kontrollerar registerinställningar och domänprincipinställningar. Föreslår principåtgärder om datorn är en del av en domän eller ändrar inställningarna till standardvärden.|
|**ResetRDPCert**|Tar bort TLS/SSL-certifikatet som är kopplat till RDP-lyssnaren och återställer RDP-lyssnarens säkerhet till standard. Använd det här skriptet om du får problem med certifikatet.|
|**SetRDPPort**|Anger standardportnumret eller det användarangivna portnumret för fjärrskrivbordsanslutningar. Aktiverar brandväggsregler för inkommande åtkomst till porten.|

## <a name="azure-cli"></a>Azure CLI

I följande exempel används kommandot [az vm run-command för](/cli/azure/vm/run-command#az_vm_run_command_invoke) att köra ett kommandoskript på en virtuell Azure Windows-dator.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure Portal

Gå till en virtuell dator i [Azure Portal](https://portal.azure.com) och välj **Kör kommando** under **ÅTGÄRDER.** Du ser en lista över tillgängliga kommandon som ska köras på den virtuella datorn.

![Lista över kommandon](./media/run-command/run-command-list.png)

Välj ett kommando som ska köras. Vissa kommandon kan ha valfria eller obligatoriska indataparametrar. För dessa kommandon visas parametrarna som textfält som du kan använda för att ange indatavärdena. För varje kommando kan du visa skriptet som körs genom att expandera **Visa skript.** **RunPowerShellScript** skiljer sig från de andra kommandona eftersom du kan ange egna anpassade skript.

> [!NOTE]
> De inbyggda kommandona kan inte redigeras.

När du har valt kommandot väljer du **Kör** för att köra skriptet. När skriptet har avslutats returneras utdata och eventuella fel i utdatafönstret. Följande skärmbild visar exempel på utdata från körning av **kommandot RDPSettings.**

![Kör utdata från kommandoskript](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

I följande exempel används cmdleten [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) för att köra ett PowerShell-skript på en virtuell Azure-dator. Cmdleten förväntar sig att skriptet som refereras i parametern är lokalt där `-ScriptPath` cmdleten körs.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Begränsa åtkomst till Run Command

Du måste ha behörighet på prenumerationsnivå för att visa körningskommandon eller `Microsoft.Compute/locations/runCommands/read` visa information om ett kommando. Den inbyggda rollen [Läsare](../../role-based-access-control/built-in-roles.md#reader) och högre nivåer har den här behörigheten.

Om du kör ett kommando krävs `Microsoft.Compute/virtualMachines/runCommand/action` behörigheten . Rollen [Virtuell datordeltagare och](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) högre nivåer har den här behörigheten.

Du kan använda en av de [inbyggda rollerna eller](../../role-based-access-control/built-in-roles.md) skapa en [anpassad roll för](../../role-based-access-control/custom-roles.md) att använda Kör kommando.

## <a name="next-steps"></a>Nästa steg

Mer information om andra sätt att köra skript och kommandon via fjärrstyrning på den virtuella datorn finns i [Köra skript på din virtuella Windows-dator.](run-scripts-in-vm.md)
