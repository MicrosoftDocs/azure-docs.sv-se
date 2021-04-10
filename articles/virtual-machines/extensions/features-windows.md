---
title: Azure VM-tillägg och-funktioner för Windows
description: Lär dig vilka tillägg som är tillgängliga för virtuella Azure-datorer, grupperade efter vad de erbjuder eller förbättrar.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/30/2018
ms.openlocfilehash: b9b10e2ed58a41cee99e7e6ef1c3994035460407
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601868"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Tillägg och funktioner för virtuella datorer för Windows

Azure-tillägg för virtuella datorer är små program som ger konfigurations- och automationsuppgifter på virtuella Azure-datorer efter distribution. Om en virtuell dator till exempel behöver programvaruinstallation, antivirusskydd eller körning av ett skript på den kan ett VM-tillägg användas. Azure VM-tillägg kan köras med Azure CLI, PowerShell, Azure Resource Manager-mallar och Azure-portalen. Tillägg kan paketeras med en ny VM-distribution eller köras mot valfritt befintligt system.

Den här artikeln innehåller en översikt över VM-tillägg, krav för att använda Azure VM-tillägg och rikt linjer för hur du identifierar, hanterar och tar bort VM-tillägg. Den här artikeln innehåller generaliserad information eftersom många VM-tillägg är tillgängliga, var och en med en potentiellt unik konfiguration. Tilläggs information finns i varje dokument som är specifikt för det enskilda tillägget.

 

## <a name="use-cases-and-samples"></a>Användnings fall och exempel

Flera olika Azure VM-tillägg är tillgängliga, var och en med ett särskilt användnings fall. Några exempel är:

- Använd PowerShell Desired State Configurations på en virtuell dator med DSC-tillägget för Windows. Mer information finns i [tillägget Azure Desired State Configuration](dsc-overview.md).
- Konfigurera övervakning av en virtuell dator med det virtuella Log Analytics agent-tillägget. Mer information finns i [ansluta virtuella Azure-datorer till Azure Monitor loggar](../../azure-monitor/vm/quick-collect-azurevm.md).
- Konfigurera en virtuell Azure-dator med hjälp av chef. Mer information finns i [Automatisera distribution av virtuella Azure-datorer med chef](/azure/developer/chef/windows-vm-configure).
- Konfigurera övervakning av din Azure-infrastruktur med Datadog-tillägget. Mer information finns i Datadog- [bloggen](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Förutom process-/regionsspecifika tillägg är ett anpassat skript tillägg tillgängligt för virtuella Windows-och Linux-datorer. Med tillägget för anpassat skript för Windows kan du köra alla PowerShell-skript på en virtuell dator. Anpassade skript är användbara för att utforma Azure-distributioner som kräver konfiguration utöver vad interna Azure-verktyg kan tillhandahålla. Mer information finns i [anpassat skript tillägg för Windows VM](custom-script-windows.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill hantera tillägget på den virtuella datorn behöver du Azure Windows-agenten installerad. Vissa enskilda tillägg har krav, till exempel åtkomst till resurser eller beroenden.

### <a name="azure-vm-agent"></a>Virtuell Azure-datoragent

Azure VM-agenten hanterar interaktioner mellan en virtuell Azure-dator och Azure Fabric-styrenheten. VM-agenten ansvarar för många funktionella aspekter av att distribuera och hantera virtuella Azure-datorer, inklusive att köra VM-tillägg. Azure VM-agenten är förinstallerad på Azure Marketplace-avbildningar och kan installeras manuellt på operativ system som stöds. Azure VM-agenten för Windows kallas Windows gästa Gent.

Information om operativ system som stöds och Installationsinstruktioner finns i [Azure Virtual Machine agent](agent-windows.md).

#### <a name="supported-agent-versions"></a>Agent versioner som stöds

För att kunna tillhandahålla bästa möjliga upplevelse finns det minimala versioner av agenten. Mer information finns i [den här artikeln](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Operativ system som stöds

Windows gästa Gent körs på flera operativ system, men tillägg ramverket har en gräns för operativ system som tillägg. Mer information finns i [den här artikeln](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
).

Vissa tillägg stöds inte för alla operativ system och genererar *felkod 51, OS som inte stöds*. Kontrol lera den enskilda tilläggs dokumentationen för support.

#### <a name="network-access"></a>Nätverksåtkomst

Tilläggs paket laddas ned från Azure Storage förlängnings lagrings plats, och överförings status för tillägg skickas till Azure Storage. Om du använder en version av agenterna som [stöds](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) , behöver du inte tillåta åtkomst till Azure Storage i VM-regionen, som kan använda agenten för att omdirigera kommunikationen till Azure Fabric Controller för agent kommunikation (HostGAPlugin-funktionen via den privilegierade kanalen på privat IP- [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)). Om du har en version som inte stöds av agenten måste du tillåta utgående åtkomst till Azure Storage i den regionen från den virtuella datorn.

> [!IMPORTANT]
> Om du har blockerat åtkomst till *168.63.129.16* med hjälp av gäst brand väggen eller med en proxy, kommer tilläggen att fungera oberoende av ovanstående. Portarna 80, 443 och 32526 krävs.

Agenter kan bara användas för att hämta tilläggs paket och rapporterings status. Om ett tillägg till exempel måste ladda ned ett skript från GitHub (anpassat skript) eller behöver åtkomst till Azure Storage (Azure Backup), måste ytterligare brand Väggs-och nätverks säkerhets grupps portar öppnas. Olika tillägg har olika krav, eftersom de är program i sin egen rätt. För tillägg som kräver åtkomst till Azure Storage eller Azure Active Directory kan du tillåta åtkomst med [Azure NSG service-Taggar](../../virtual-network/network-security-groups-overview.md#service-tags) till Storage eller AzureActiveDirectory.

Gäst agenten i Windows har inte stöd för att dirigera om agent trafik begär Anden via, vilket innebär att gäst agenten i Windows kommer att förlita sig på din anpassade proxy (om du har en) för att komma åt resurser på Internet eller på värden via IP-168.63.129.16.

## <a name="discover-vm-extensions"></a>Identifiera VM-tillägg

Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. Om du vill se en fullständig lista använder du [Get-AzVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage). I följande exempel visas alla tillgängliga tillägg på platsen för *västkusten* :

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Kör VM-tillägg

VIRTUELLA Azure-tillägg körs på befintliga virtuella datorer, vilket är användbart när du behöver göra konfigurations ändringar eller återställa anslutningar på en redan distribuerad virtuell dator. VM-tillägg kan också paketeras med Azure Resource Manager mallar distributioner. Med hjälp av tillägg med Resource Manager-mallar kan virtuella Azure-datorer distribueras och konfigureras utan åtgärder efter distribution.

Följande metoder kan användas för att köra ett tillägg mot en befintlig virtuell dator.

### <a name="powershell"></a>PowerShell

Det finns flera PowerShell-kommandon för att köra enskilda tillägg. Om du vill se en lista använder du [Get-Command](/powershell/module/microsoft.powershell.core/get-command) och filter för *tillägg*:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Detta ger utdata som liknar följande:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

I följande exempel används det anpassade skript tillägget för att ladda ned ett skript från en GitHub-lagringsplats till den virtuella mål datorn och sedan köra skriptet. Mer information om det anpassade skript tillägget finns i [Översikt över anpassat skript tillägg](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

I följande exempel används VM Access-tillägget för att återställa det administrativa lösen ordet för en virtuell Windows-dator till ett tillfälligt lösen ord. Mer information om åtkomst tillägget för virtuella datorer finns i [återställa fjärr skrivbords tjänster på en virtuell Windows-dator](/troubleshoot/azure/virtual-machines/reset-rdp). När du har kört detta bör du återställa lösen ordet vid första inloggningen:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzVMExtension`Kommandot kan användas för att starta alla VM-tillägg. Mer information finns i [set-AzVMExtension-referensen](/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Azure Portal

VM-tillägg kan tillämpas på en befintlig virtuell dator via Azure Portal. Välj den virtuella datorn i portalen, Välj **tillägg** och välj sedan **Lägg till**. Välj det tillägg du vill använda i listan över tillgängliga tillägg och följ anvisningarna i guiden.

I följande exempel visas installationen av tillägget Microsoft Antimalware från Azure Portal:

![Installera tillägg för program mot skadlig kod](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till i en Azure Resource Manager mall och köras med mallen. När du distribuerar ett tillägg med en mall kan du skapa fullständigt konfigurerade Azure-distributioner. Följande JSON tas till exempel från en Resource Manager-mall och distribuerar en uppsättning belastningsutjämnade virtuella datorer och en Azure SQL Database och installerar sedan ett .NET Core-program på varje virtuell dator. VM-tillägget tar hand om program varu installationen.

Mer information finns i [fullständig Resource Manager-mall](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Mer information om hur du skapar Resource Manager-mallar finns i [redigera Azure Resource Manager mallar med Windows VM-tillägg](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Säkra data för VM-tillägg

När du kör ett VM-tillägg kan det vara nödvändigt att inkludera känslig information som autentiseringsuppgifter, lagrings konto namn och åtkomst nycklar för lagrings kontot. Många VM-tillägg innehåller en skyddad konfiguration som krypterar data och bara dekrypterar den i den virtuella mål datorn. Varje tillägg har ett särskilt skyddat konfigurations schema och var och en beskrivs i den tilläggsbaserade dokumentationen.

I följande exempel visas en instans av det anpassade skript tillägget för Windows. Kommandot som ska köras innehåller en uppsättning autentiseringsuppgifter. I det här exemplet krypteras inte kommandot som ska köras:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Om du flyttar **kommandot för att köra** egenskapen till den **skyddade** konfigurationen skyddas körnings strängen, som du ser i följande exempel:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

På en virtuell Azure IaaS-dator som använder tillägg i certifikat konsolen kan du se certifikat som har **_certifikats generatorn Windows Azure CRP_**. På en klassisk RDFE-VM har dessa certifikat ämnes namnet **_Windows Azure Service Management för tillägg_**.

Dessa certifikat skyddar kommunikationen mellan den virtuella datorn och dess värd under överföringen av skyddade inställningar (lösenord och andra autentiseringsuppgifter) som används av tillägg. Certifikaten skapas av Azure-infrastrukturkontrollanten och skickas till den virtuella datoragenten. Om du stoppar och startar den virtuella datorn varje dag kan ett nytt certifikat skapas av infrastrukturkontrollanten. Certifikatet lagras i datorns personliga certifikatarkiv. Dessa certifikat kan tas bort. VM-agenten återskapar certifikat vid behov.

### <a name="how-do-agents-and-extensions-get-updated"></a>Hur uppdateras agenter och tillägg?

Agenterna och tilläggen delar samma uppdaterings funktion. Vissa uppdateringar kräver inga ytterligare brand Väggs regler.

När det finns en tillgänglig uppdatering installeras den bara på den virtuella datorn när tilläggen ändras och andra ändringar i VM-modellen, till exempel:

- Datadiskar
- Tillägg
- Behållare för startdiagnostik
- Gäst operativ system hemligheter
- Storlek på virtuell dator
- Nätverksprofil

Utgivare gör uppdateringar tillgängliga för regioner vid olika tidpunkter, så det är möjligt att du kan ha virtuella datorer i olika regioner i olika versioner.

#### <a name="listing-extensions-deployed-to-a-vm"></a>List tillägg som har distribuerats till en virtuell dator

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Agent uppdateringar

Windows-gäst agenten innehåller bara *tillägg hanterings kod*, *Windows etablerings koden* är separat. Du kan avinstallera Windows gästa Gent. Det går inte att inaktivera den automatiska uppdateringen av fönster gäst agenten.

*Tilläggs hanterings koden* ansvarar för kommunikation med Azure-infrastrukturen och hanterar de åtgärder för VM-tillägg som installation, rapporterings status, uppdaterar enskilda tillägg och tar bort dem. Uppdateringar innehåller säkerhets korrigeringar, fel korrigeringar och förbättringar i *tilläggs hanterings koden*.

Information om hur du kontrollerar vilken version du kör finns i [identifiera installerad Windows gästa Gent](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Tilläggs uppdateringar

När en tilläggs uppdatering är tillgänglig laddar Windows gäst agent ned och uppgraderar tillägget. Automatiska tilläggs uppdateringar är antingen *mindre* eller *snabb korrigeringar*. Du kan välja om du vill inaktivera tillägg som är *mindre* uppdateringar när du etablerar tillägget. I följande exempel visas hur du automatiskt uppgraderar lägre versioner i en Resource Manager-mall med *aktiverat autoupgrademinorversion ": true"*:

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

För att få de senaste fel korrigeringarna för smärre versioner, rekommenderar vi att du alltid väljer automatisk uppdatering i dina tillägg-distributioner. Uppdateringar av snabb korrigeringar som innehåller säkerhets-eller nyckel fel korrigeringar kan inte avregistreras.

### <a name="how-to-identify-extension-updates"></a>Så här identifierar du tilläggs uppdateringar

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifiera om tillägget har angetts med aktiverat autoupgrademinorversion på en virtuell dator

Du kan se från VM-modellen om tillägget etablerades med ' aktiverat autoupgrademinorversion '. Om du vill kontrol lera detta använder du [Get-AzVm](/powershell/module/az.compute/get-azvm) och anger resurs gruppen och namnet på den virtuella datorn enligt följande:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Följande exempel på utdata visar att *aktiverat autoupgrademinorversion* är inställt på *True*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifiera när ett aktiverat autoupgrademinorversion har inträffat

Om du vill se när en uppdatering av tillägget har inträffat granskar du agent loggarna på den virtuella datorn på *C:\WindowsAzure\Logs\WaAppAgent.log*

I följande exempel hade den virtuella datorn *Microsoft. Compute. CustomScriptExtension 1,8* installerat. En snabb korrigering är tillgänglig för version *1,9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Agent behörigheter

För att utföra dess uppgifter måste agenten köras som *lokalt system*.

## <a name="troubleshoot-vm-extensions"></a>Felsöka VM-tillägg

Varje VM-tillägg kan ha fel söknings steg som är speciella för tillägget. Om du till exempel använder tillägget för anpassat skript kan du hitta skript körnings information lokalt på den virtuella dator där tillägget kördes. Eventuella tilläggs fel söknings steg beskrivs i den särskilda dokumentationen.

Följande fel söknings steg gäller för alla VM-tillägg.

1. Om du vill kontrol lera loggen för Windows gäst agent tittar du på aktiviteten när ditt tillägg har allokerats i *C:\WindowsAzure\Logs\WaAppAgent.log*

2. Se de faktiska tilläggs loggarna för mer information *i \\ <extensionName> C:\WindowsAzure\Logs\Plugins*

3. Sök efter felkoder, kända problem och fel söknings avsnitt för specifika dokumentation

4. Titta på system loggarna. Kontrol lera om det finns andra åtgärder som kan ha stör tillägget, till exempel en tids krävande installation av ett annat program som kräver exklusiv åtkomst till paket hanteraren.

### <a name="common-reasons-for-extension-failures"></a>Vanliga orsaker till tilläggs problem

1. Tilläggen har 20 minuter att köra (undantag är CustomScript-tillägg, chef och DSC som har 90 minuter). Om distributionen överskrider den här tiden markeras den som en tids gräns. Orsaken till detta kan bero på att det finns låg resurs för virtuella datorer, andra VM-konfigurationer/starta aktiviteter som förbrukar stora mängder resurser, medan tillägget försöker etablera.

2. Minimi kraven är inte uppfyllda. Vissa tillägg har beroenden på VM SKU: er, till exempel HPC-avbildningar. Tillägg kan kräva vissa krav på nätverks åtkomst, t. ex. kommunikation med Azure Storage eller offentliga tjänster. Andra exempel kan vara till gång till paket lagrings utrymmen, ta slut på disk utrymme eller säkerhets begränsningar.

3. Exklusiv åtkomst till paket hanteraren. I vissa fall kan det uppstå en tids krävande konfiguration av virtuella datorer och tillägg i konflikt, där de båda behöver exklusiv åtkomst till paket hanteraren.

### <a name="view-extension-status"></a>Visa tilläggs status

När ett VM-tillägg har körts mot en virtuell dator använder du [Get-AzVM](/powershell/module/az.compute/get-azvm) för att returnera tilläggs status. *Status för under status [0]* visar att tillägget har slutförts, vilket innebär att det lyckades distribueras till den virtuella datorn, men körningen av tillägget i den virtuella datorn misslyckades, under *status [1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Utdata liknar följande exempel på utdata:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

Du kan också hitta körnings status för tillägg i Azure Portal. Om du vill visa status för ett tillägg väljer du den virtuella datorn, väljer **tillägg** och väljer sedan önskat tillägg.

### <a name="rerun-vm-extensions"></a>Kör om VM-tillägg

Det kan finnas fall där ett VM-tillägg måste köras igen. Du kan köra ett tillägg igen genom att ta bort det och sedan köra tillägget igen med en körnings metod som du väljer. Om du vill ta bort ett tillägg använder du [Remove-AzVMExtension](/powershell/module/az.compute/remove-azvmextension) på följande sätt:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Du kan också ta bort ett tillägg i Azure Portal på följande sätt:

1. Välj en virtuell dator.
2. Välj **tillägg**.
3. Välj önskat tillägg.
4. Välj **Avinstallera**.

## <a name="common-vm-extensions-reference"></a>Common VM Extensions-referens
| Tilläggs namn | Beskrivning | Mer information |
| --- | --- | --- |
| Anpassat skripttillägg för Windows |Kör skript mot en virtuell Azure-dator |[Anpassat skripttillägg för Windows](custom-script-windows.md) |
| DSC-tillägg för Windows |PowerShell DSC (Desired State Configuration)-tillägg |[DSC-tillägg för Windows](dsc-overview.md) |
| Azure Diagnostics-tillägg |Hantera Azure-diagnostik |[Azure-diagnostik tillägg](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Tillägg för Azure VM Access |Hantera användare och autentiseringsuppgifter |[Åtkomst tillägg för virtuella datorer för Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Nästa steg

Mer information om tillägg för virtuella datorer finns i [Översikt över virtuella Azure-datorer och funktioner](overview.md).