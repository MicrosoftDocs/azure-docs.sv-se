---
title: Kör PowerShell-skript i en virtuell Windows-dator i Azure
description: I det här avsnittet beskrivs hur du kör PowerShell-skript i en virtuell Azure Windows-dator med hjälp av kommando funktionen kör
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: e2cd8ee4095db235215a2beaa68975e819b474c1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560692"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Kör PowerShell-skript i din virtuella Windows-dator med hjälp av kommandot kör

Funktionen Kör kommando använder agenten för virtuella datorer (VM) för att köra PowerShell-skript i en virtuell Azure Windows-dator. Du kan använda de här skripten till allmän hantering av datorer eller program. De kan hjälpa dig att snabbt diagnostisera och åtgärda åtkomst till virtuella datorer och nätverks problem och återställa den virtuella datorn till ett lyckat tillstånd.



## <a name="benefits"></a>Fördelar

Du kan komma åt dina virtuella datorer på flera olika sätt. Kommandot kör kan köra skript på dina virtuella datorer via en fjärr anslutning med hjälp av VM-agenten. Du använder kommandot kör via Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)eller [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) för virtuella Windows-datorer.

Den här funktionen är användbar i alla scenarier där du vill köra ett skript i en virtuell dator. Det är ett av de enda sätt som du kan använda för att felsöka och reparera en virtuell dator som inte har RDP-eller SSH-porten öppen på grund av en felaktig konfiguration av nätverks-eller administrativa användare.

## <a name="restrictions"></a>Begränsningar

Följande begränsningar gäller när du använder Kör kommando:

* Utdata är begränsade till de senaste 4 096 byten.
* Minimi tiden att köra ett skript är ungefär 20 sekunder.
* Skript körs som system i Windows.
* Ett skript i taget kan köras.
* Skript som efterfrågar information (interaktivt läge) stöds inte.
* Du kan inte avbryta ett skript som körs.
* Den längsta tid som ett skript kan köras är 90 minuter. Efter det kommer timeout att uppnås.
* Utgående anslutningar från den virtuella datorn krävs för att returnera resultatet av skriptet.
* Vi rekommenderar inte att du kör ett skript som gör att VM-agenten stoppas eller uppdateras. Detta kan ge tillägget i ett över gångs tillstånd, vilket leder till en tids gräns.

> [!NOTE]
> För att funktionen ska fungera korrekt måste körnings kommandot ansluta (port 443) till offentliga Azure-IP-adresser. Om tillägget inte har åtkomst till dessa slut punkter kan skripten köras utan problem, men inga resultat returneras. Om du blockerar trafik på den virtuella datorn kan du använda [service märken](../../virtual-network/network-security-groups-overview.md#service-tags) för att tillåta trafik till offentliga Azure-IP-adresser med hjälp av `AzureCloud` taggen.

## <a name="available-commands"></a>Tillgängliga kommandon

Den här tabellen visar en lista över kommandon som är tillgängliga för virtuella Windows-datorer. Du kan använda kommandot **RunPowerShellScript** för att köra anpassade skript som du vill ha. När du använder Azure CLI eller PowerShell för att köra ett kommando måste värdet som du anger för `--command-id` `-CommandId` parametern eller vara något av följande värden i listan. När du anger ett värde som inte är ett tillgängligt kommando visas följande fel meddelande:

```error
The entity was not found in this Azure location
```

|**Namn**|**Beskrivning**|
|---|---|
|**RunPowerShellScript**|Kör ett PowerShell-skript.|
|**EnableRemotePS**|Konfigurerar datorn för att aktivera fjärr-PowerShell.|
|**EnableAdminAccount**|Kontrollerar om det lokala administratörs kontot är inaktiverat och om så är möjligt.|
|**Config**| Visar detaljerad information om IP-adressen, nät masken och standard-gatewayen för varje nätverkskort som är kopplat till TCP/IP.|
|**RDPSettings**|Kontrollerar register inställningar och domän princip inställningar. Föreslår princip åtgärder om datorn tillhör en domän eller ändrar inställningarna till standardvärdena.|
|**ResetRDPCert**|Tar bort TLS/SSL-certifikatet som är kopplat till RDP-lyssnaren och återställer RDP Listener Security till standard. Använd det här skriptet om du ser några problem med certifikatet.|
|**SetRDPPort**|Anger standard port numret eller det användardefinierade port numret för fjärr skrivbords anslutningar. Aktiverar brand Väggs regler för inkommande åtkomst till porten.|

## <a name="azure-cli"></a>Azure CLI

I följande exempel används kommandot [AZ VM Run-Command](/cli/azure/vm/run-command#az-vm-run-command-invoke) för att köra ett gränssnitts skript på en virtuell Azure Windows-dator.

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

Gå till en virtuell dator i [Azure Portal](https://portal.azure.com) och välj **Kör kommando** under **åtgärder**. Du ser en lista över tillgängliga kommandon som ska köras på den virtuella datorn.

![Lista med kommandon](./media/run-command/run-command-list.png)

Välj ett kommando som ska köras. Några av kommandona kan ha valfria eller obligatoriska indataparametrar. För dessa kommandon visas parametrarna som textfält så att du kan ange indatavärdena. Du kan visa skriptet som körs genom att expandera **visnings skript** för varje kommando. **RunPowerShellScript** skiljer sig från de andra kommandona, eftersom det gör att du kan ange ett eget anpassat skript.

> [!NOTE]
> De inbyggda kommandona kan inte redige ras.

När du har valt kommandot väljer du **Kör** för att köra skriptet. När skriptet har slutförts returneras utdata och eventuella fel i fönstret utdata. Följande skärm bild visar ett exempel på utdata från att köra kommandot **RDPSettings** .

![Kör utdata från kommando skript](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

I följande exempel används cmdleten [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) för att köra ett PowerShell-skript på en virtuell Azure-dator. Cmdleten förväntar sig att skriptet som refereras till i `-ScriptPath` parametern ska vara lokalt till den plats där cmdleten körs.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Begränsa åtkomst till Run Command

Att Visa körnings kommandon eller Visa information om ett kommando kräver `Microsoft.Compute/locations/runCommands/read` behörighet på prenumerations nivå. Den inbyggda [läsar](../../role-based-access-control/built-in-roles.md#reader) rollen och högre nivåer har den här behörigheten.

Du måste ha behörighet för att köra ett kommando `Microsoft.Compute/virtualMachines/runCommand/action` . Rollen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) och högre nivåer har den här behörigheten.

Du kan använda en av de [inbyggda rollerna](../../role-based-access-control/built-in-roles.md) eller skapa en [anpassad roll](../../role-based-access-control/custom-roles.md) för att använda kör-kommandot.

## <a name="next-steps"></a>Nästa steg

Information om andra sätt att köra skript och kommandon via fjärr anslutning i den virtuella datorn finns i [köra skript i din virtuella Windows-dator](run-scripts-in-vm.md).
