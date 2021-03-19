---
title: Översikt av Azure VM-agent
description: Översikt av Azure VM-agent
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 07/20/2019
ms.openlocfilehash: 33db214013111b0dd4540a1b1d2947b7d1854db9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607491"
---
# <a name="azure-virtual-machine-agent-overview"></a>Översikt över Azure Virtual Machine agent
Den Microsoft Azure virtuella dator agenten (VM-agenten) är en säker, lätt process som hanterar interaktionen mellan virtuella datorer (VM) med Azure Fabric-styrenheten. VM-agenten har en primär roll för att aktivera och köra tillägg för virtuella Azure-datorer. Med VM-tillägg kan du konfigurera virtuella datorer efter distributionen, till exempel installera och konfigurera program vara. VM-tillägg möjliggör också återställnings funktioner som att återställa det administrativa lösen ordet för en virtuell dator. Utan Azure VM-agenten kan VM-tillägg inte köras.

Den här artikeln innehåller information om installation och identifiering av Azure Virtual Machine-agenten.

## <a name="install-the-vm-agent"></a>Installera VM-agenten

### <a name="azure-marketplace-image"></a>Azure Marketplace-avbildning

Azure VM-agenten installeras som standard på alla virtuella Windows-datorer som distribueras från en Azure Marketplace-avbildning. När du distribuerar en Azure Marketplace-avbildning från portalen, PowerShell, kommando rads gränssnittet eller en Azure Resource Manager mall, installeras även Azure VM-agenten.

Windows gäst agent-paketet är uppdelat i två delar:

- Etablerings agent (PA)
- Windows gästa Gent (Vinga)

Om du vill starta en virtuell dator måste du ha PA installerat på den virtuella datorn, men vingen behöver inte vara installerad. På den virtuella datorns distributions tid kan du välja att inte installera vingen. I följande exempel visas hur du väljer alternativet *provisionVmAgent* med en mall för Azure Resource Manager:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Om du inte har agenterna installerade kan du inte använda vissa Azure-tjänster, till exempel Azure Backup eller Azure-säkerhet. Dessa tjänster kräver att ett tillägg installeras. Om du har distribuerat en virtuell dator utan att Vinga kan du installera den senaste versionen av agenten senare.

### <a name="manual-installation"></a>Manuell installation
Windows VM-agenten kan installeras manuellt med ett Windows Installer-paket. Manuell installation kan vara nödvändigt när du skapar en anpassad VM-avbildning som distribueras till Azure. Om du vill installera den virtuella Windows-agenten manuellt [laddar du ned installations programmet för VM-agenten](https://go.microsoft.com/fwlink/?LinkID=394789). Du kan också söka efter en speciell version i [GitHub Windows IaaS VM agent releases](https://github.com/Azure/WindowsVMAgent/releases). VM-agenten stöds på Windows Server 2008 (64 bitar) och senare.

> [!NOTE]
> Det är viktigt att uppdatera alternativet AllowExtensionOperations när du har installerat VMAgent manuellt på en virtuell dator som har distribuerats från avbildningen utan att ProvisionVMAgent aktivera.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Förutsättningar

- Windows VM-agenten måste ha minst Windows Server 2008 SP2 (64-bitars) för att kunna köras, med .NET Framework 4,0. Se [lägsta versions stöd för virtuella dator agenter i Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

- Se till att den virtuella datorn har åtkomst till IP-168.63.129.16. Mer information finns i [Vad är IP-168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

- Se till att DHCP är aktiverat i den virtuella gäst datorn. Detta krävs för att hämta värden eller infrastruktur resurser från DHCP för att IaaS VM-agenten och tillägg ska fungera. Om du behöver en statisk privat IP-adress bör du konfigurera den via Azure Portal eller PowerShell och kontrol lera att DHCP-alternativet i den virtuella datorn är aktiverat. [Lär dig mer](../../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) om att konfigurera en statisk IP-adress med PowerShell.


## <a name="detect-the-vm-agent"></a>Identifiera VM-agenten

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell-modulen kan användas för att hämta information om virtuella Azure-datorer. Använd [Get-AzVM](/powershell/module/az.compute/get-azvm)för att se information om en virtuell dator, till exempel etablerings status för den virtuella Azure-agenten:

```powershell
Get-AzVM
```

I följande komprimerade exempel utdata visas egenskapen *ProvisionVMAgent* inkapslad i `OSProfile` . Den här egenskapen kan användas för att avgöra om VM-agenten har distribuerats till den virtuella datorn:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Följande skript kan användas för att returnera en kortare lista över VM-namn och tillstånd för VM-agenten:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Manuell identifiering

När du är inloggad på en virtuell Windows-dator kan du använda aktivitets hanteraren för att undersöka aktiva processer. Om du vill söka efter den virtuella Azure-agenten öppnar du aktivitets hanteraren, klickar på fliken *information* och letar efter ett process namn **WindowsAzureGuestAgent.exe**. Förekomsten av den här processen indikerar att VM-agenten är installerad.


## <a name="upgrade-the-vm-agent"></a>Uppgradera VM-agenten
Azure VM-agenten för Windows uppgraderas automatiskt på avbildningar som distribueras från Azure Marketplace. När nya virtuella datorer distribueras till Azure får de den senaste virtuella dator agenten vid etablerings tiden för den virtuella datorn. Om du har installerat agenten manuellt eller distribuerar anpassade VM-avbildningar måste du manuellt uppdatera för att inkludera den nya VM-agenten när avbildningen skapas.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Samling med automatiska loggar i Windows gästa Gent
Windows gästa Gent har en funktion för att automatiskt samla in vissa loggar. Den här funktionen är kontrollant i CollectGuestLogs.exe processen. Det finns för både PaaS-Cloud Services och IaaS Virtual Machines och målet är att snabbt & automatiskt samla in vissa diagnostikloggar från en virtuell dator – så att de kan användas för offline-analys. De insamlade loggarna är händelse loggar, OS-loggar, Azure-loggar och vissa register nycklar. Den skapar en ZIP-fil som överförs till den virtuella datorns värd. Den här ZIP-filen kan sedan ses av teknik team och support tekniker för att undersöka problem på begäran av kunden som äger den virtuella datorn.

## <a name="guest-agent-and-osprofile-certificates"></a>Gästa Gent-och OSProfile-certifikat
Azure VM-agenten ansvarar för att installera certifikaten som refereras till i `OSProfile` en virtuell dator eller skalnings uppsättning för virtuella datorer. Om du tar bort dessa certifikat manuellt från MMC-konsolen för certifikat i den virtuella gäst datorn förväntas gäst agenten att lägga till dem igen.
Om du vill ta bort ett certifikat permanent måste du ta bort det från och `OSProfile` sedan ta bort det från gäst operativ systemet.

Använd [Remove-AzVMSecret]() för en virtuell dator för att ta bort certifikat från `OSProfile` .

Mer information om certifikat för skalnings uppsättningar för virtuella datorer finns i [Virtual Machine Scale Sets-hur gör jag för att ta bort föråldrade certifikat?](../../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-remove-deprecated-certificates)


## <a name="next-steps"></a>Nästa steg
Mer information om tillägg för virtuella datorer finns i [Översikt över virtuella Azure-datorer och funktioner](overview.md).
