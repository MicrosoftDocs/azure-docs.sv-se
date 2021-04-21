---
title: Azure VM-tillägg och -funktioner för Linux
description: Lär dig vilka tillägg som är tillgängliga för virtuella Azure-datorer i Linux, grupperade efter vad de tillhandahåller eller förbättrar.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/30/2018
ms.openlocfilehash: bdbbc4c421b83fd041c7d900fb0edd01c4d636e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785099"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Tillägg och funktioner för virtuella datorer för Linux

Azure-tillägg för virtuella datorer är små program som ger konfigurations- och automationsuppgifter på virtuella Azure-datorer efter distribution. Om en virtuell dator till exempel behöver programvaruinstallation, antivirusskydd eller körning av ett skript på den kan ett VM-tillägg användas. Azure VM-tillägg kan köras med Azure CLI, PowerShell, Azure Resource Manager-mallar och Azure-portalen. Tillägg kan paketeras med en ny VM-distribution eller köras mot valfritt befintligt system.

Den här artikeln innehåller en översikt över VM-tillägg, krav för att använda Azure VM-tillägg och vägledning om hur du identifierar, hanterar och tar bort VM-tillägg. Den här artikeln innehåller generaliserad information eftersom många VM-tillägg är tillgängliga, var och en med en potentiellt unik konfiguration. Tilläggsspecifik information finns i varje dokument som är specifikt för det enskilda tillägget.

## <a name="use-cases-and-samples"></a>Användningsfall och exempel

Det finns flera olika Azure VM-tillägg, var och en med ett specifikt användningsfall. Några exempel är:

- Använd PowerShell Desired State-konfigurationer på en virtuell dator med DSC-tillägget för Linux. Mer information finns i [Azure Desired State-konfigurationstillägget](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurera övervakning av en virtuell dator med vm-tillägget Microsoft Monitoring Agent. Mer information finns i Så [här övervakar du en virtuell Linux-dator.](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)
- Konfigurera övervakning av Din Azure-infrastruktur med tillägget Chef eller Datadog. Mer information finns i [Chef-dokumentation eller](https://docs.chef.io/azure_portal.html) [Datadog-bloggen](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Förutom processspecifika tillägg är ett tillägg för anpassat skript tillgängligt för både virtuella Windows- och Linux-datorer. Tillägget för anpassat skript för Linux tillåter att alla Bash-skript körs på en virtuell dator. Anpassade skript är användbara för att utforma Azure-distributioner som kräver konfiguration utöver vad inbyggda Azure-verktyg kan ge. Mer information finns i Anpassat [skripttillägg för virtuella Linux-datorer.](custom-script-linux.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill hantera tillägget på den virtuella datorn måste Azure Linux-agenten vara installerad. Vissa enskilda tillägg har förutsättningar, till exempel åtkomst till resurser eller beroenden.

### <a name="azure-vm-agent"></a>Virtuell Azure-datoragent

Azure VM-agenten hanterar interaktioner mellan en virtuell Azure-dator och Azure-infrastrukturkontrollanten. VM-agenten ansvarar för många funktionella aspekter av distribution och hantering av virtuella Azure-datorer, inklusive att köra VM-tillägg. Azure VM-agenten är förinstallerad på Azure Marketplace-avbildningar och kan installeras manuellt på operativsystem som stöds. Azure VM-agenten för Linux kallas Linux-agenten.

Information om operativsystem som stöds och installationsinstruktioner finns i [Agent för virtuella Azure-datorer.](agent-linux.md)

#### <a name="supported-agent-versions"></a>Agentversioner som stöds

För att ge bästa möjliga upplevelse finns det lägsta versioner av agenten. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>OS:er som stöds

Linux-agenten körs på flera operativsystem, men ramverket för tillägg har en gräns för operativsystemen som tillägg. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Vissa tillägg stöds inte i alla operativsystem och kan generera *felkod 51, "OS stöds inte".* Läs dokumentationen om enskilda tillägg för support.

#### <a name="network-access"></a>Nätverksåtkomst

Tilläggspaket laddas ned från Azure Storage lagringsplatsen för tillägg och uppladdningar av tilläggsstatus publiceras till Azure Storage. Om du använder den [version](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) av agenterna som stöds behöver du inte tillåta åtkomst till Azure Storage i VM-regionen, och du kan också använda agenten för att omdirigera kommunikationen till Azure-infrastrukturkontrollanten för agentkommunikation. Om du har en version av agenten som inte stöds måste du tillåta utgående åtkomst till Azure Storage i den regionen från den virtuella datorn.

> [!IMPORTANT]
> Om du har blockerat åtkomsten *till 168.63.129.16* med hjälp av gästbrandväggen misslyckas tilläggen oavsett ovanstående.

Agenter kan bara användas för att ladda ned tilläggspaket och rapportera status. Om en tilläggsinstallation till exempel behöver ladda ned ett skript från GitHub (anpassat skript) eller behöver åtkomst till Azure Storage (Azure Backup) måste ytterligare portar för brandvägg/nätverkssäkerhetsgrupp öppnas. Olika tillägg har olika krav, eftersom de är program på egen hand. För tillägg som kräver åtkomst till Azure Storage kan du tillåta åtkomst med hjälp av Azure NSG-tjänsttaggar för [Storage](../../virtual-network/network-security-groups-overview.md#service-tags).

Linux-agenten har stöd för proxyserver för att omdirigera begäranden om agenttrafik. Stöd för den här proxyservern gäller dock inte tillägg. Du måste konfigurera varje enskilt tillägg så att det fungerar med en proxyserver.

## <a name="discover-vm-extensions"></a>Identifiera VM-tillägg

Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. Om du vill se en fullständig lista använder du [az vm extension image list](/cli/azure/vm/extension/image#az_vm_extension_image_list). I följande exempel visas alla tillgängliga tillägg på *platsen USA,* västra:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Köra VM-tillägg

Azure VM-tillägg körs på befintliga virtuella datorer, vilket är användbart när du behöver göra konfigurationsändringar eller återställa anslutningen på en redan distribuerad virtuell dator. VM-tillägg kan också paketeras med Azure Resource Manager malldistributioner. Med tillägg med Resource Manager-mallar kan virtuella Azure-datorer distribueras och konfigureras utan åtgärder efter distributionen.

Följande metoder kan användas för att köra ett tillägg mot en befintlig virtuell dator.

### <a name="azure-cli"></a>Azure CLI

Azure VM-tillägg kan köras mot en befintlig virtuell dator med [kommandot az vm extension set.](/cli/azure/vm/extension#az_vm_extension_set) I följande exempel körs det anpassade skripttillägget mot en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup*. Ersätt exempelresursgruppens namn, VM-namn och skript som ska köras (https: \/ /raw.githubusercontent.com/me/project/hello.sh) med din egen information. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

När tillägget körs korrekt liknar utdata följande exempel:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

VM-tillägg kan tillämpas på en befintlig virtuell dator via Azure Portal. Välj den virtuella datorn i portalen, välj **Tillägg och** välj sedan Lägg **till**. Välj det tillägg som du vill använda i listan över tillgängliga tillägg och följ anvisningarna i guiden.

Följande bild visar installationen av tillägget för linux-anpassat skript från Azure Portal:

![Installera tillägg för anpassat skript](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till Azure Resource Manager en mall och köras med distributionen av mallen. När du distribuerar ett tillägg med en mall kan du skapa fullständigt konfigurerade Azure-distributioner. Följande JSON tas till exempel från en Resource Manager-mall som distribuerar en uppsättning belastningsutjämnade virtuella datorer och Azure SQL Database och installerar sedan ett .NET Core-program på varje virtuell dator. VM-tillägget tar hand om programvaruinstallationen.

Mer information finns i den fullständiga [Resource Manager mallen](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Mer information om hur du skapar Resource Manager-mallar finns [i Redigera Azure Resource Manager mallar](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Skydda data för VM-tillägg

När du kör ett VM-tillägg kan det vara nödvändigt att inkludera känslig information, till exempel autentiseringsuppgifter, lagringskontonamn och åtkomstnycklar för lagringskonto. Många VM-tillägg innehåller en skyddad konfiguration som krypterar data och endast dekrypterar dem i den virtuella måldatorn. Varje tillägg har ett specifikt schema för skyddad konfiguration, och vart och ett beskrivs i tilläggsspecifik dokumentation.

I följande exempel visas en instans av tillägget för anpassat skript för Linux. Kommandot som ska köras innehåller en uppsättning autentiseringsuppgifter. I det här exemplet är kommandot som ska köras inte krypterat:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Om du **flyttar kommandot för att** köra egenskapen till **den** skyddade konfigurationen skyddas körningssträngen, som du ser i följande exempel:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>Hur uppdateras agenter och tillägg?

Agenterna och tilläggen delar samma uppdateringsmekanism. Vissa uppdateringar kräver inte ytterligare brandväggsregler.

När en uppdatering är tillgänglig installeras den bara på den virtuella datorn när det sker en ändring av tillägg och andra ändringar i VM-modellen, till exempel:

- Datadiskar
- Tillägg
- Startdiagnostikcontainer
- Hemligheter för gästoperativsystem
- Storlek på virtuell dator
- Nätverksprofil

Utgivare gör uppdateringar tillgängliga för regioner vid olika tidpunkter, så det är möjligt att du kan ha virtuella datorer i olika regioner i olika versioner.

#### <a name="agent-updates"></a>Agentuppdateringar

Linux VM-agenten *innehåller etableringsagentkod och* *tilläggshanteringskod i*  ett paket som inte kan separeras. Du kan inaktivera *etableringsagenten* när du vill etablera i Azure med hjälp av cloud-init. Information om hur du gör detta [finns i använda cloud-init](../linux/using-cloud-init.md).

Versioner av agenterna som stöds kan använda automatiska uppdateringar. Den enda kod som kan uppdateras är *tilläggshanteringskoden*, inte etableringskoden. *Etableringsagentens kod* körs en gång.

*Tilläggshanteringskoden* ansvarar för att kommunicera med Azure-infrastrukturerna och hantera ÅTGÄRDER för VM-tillägg, till exempel installationer, rapporteringsstatus, uppdatering av enskilda tillägg och borttagning av dem. Uppdateringar innehåller säkerhetskorrigeringar, felkorrigeringar och förbättringar av *tilläggshanteringskoden*.

När agenten har installerats skapas en överordnad daemon. Den överordnade skapar sedan en underordnad process som används för att hantera tillägg. Om en uppdatering är tillgänglig för agenten laddas den ned, den överordnade stoppar den underordnade processen, uppgraderar den och startar sedan om den. Om det uppstår problem med uppdateringen återställs den överordnade processen till den tidigare underordnade versionen.

Den överordnade processen kan inte uppdateras automatiskt. Det överordnade paketet kan bara uppdateras med en distributionspaketuppdatering.

Kontrollera vilken version du kör på `waagent` följande sätt:

```bash
waagent --version
```

Utdata ser ut ungefär så här:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.6.0
Goal state agent: 2.2.18
```

I föregående exempelutdata är den överordnade eller "paket distribuerade versionen" *WALinuxAgent-2.2.17*

Måltillståndsagenten är den automatiska uppdateringsversionen.

Vi rekommenderar starkt att du alltid har automatisk uppdatering för [agenten, AutoUpdate.Enabled=y](./update-linux-agent.md). Om detta inte är aktiverat måste du fortsätta att uppdatera agenten manuellt och inte få fel- och säkerhetskorrigeringar.

#### <a name="extension-updates"></a>Tilläggsuppdateringar

När en tilläggsuppdatering är tillgänglig laddar Linux-agenten ned och uppgraderar tillägget. Automatiska tilläggsuppdateringar är antingen *Mindre eller* *Snabbkorrigering.* Du kan välja att använda eller välja bort tillägg *Mindre* uppdateringar när du etablerar tillägget. I följande exempel visas hur du automatiskt uppgraderar mindre versioner i en Resource Manager mall *med autoUpgradeMinorVersion": true,'*:

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

För att få de senaste mindre felkorrigeringarna rekommenderar vi starkt att du alltid väljer automatisk uppdatering i dina tilläggsdistributioner. Snabbkorrigeringsuppdateringar som innehåller säkerhets- eller nyckelfelkorrigeringar kan inte väljas bort.

### <a name="how-to-identify-extension-updates"></a>Så här identifierar du tilläggsuppdateringar

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifiera om tillägget har angetts med autoUpgradeMinorVersion på en virtuell dator

Du kan se från VM-modellen om tillägget har etablerats med "autoUpgradeMinorVersion". Du kan kontrollera detta genom [att använda az vm show](/cli/azure/vm#az_vm_show) och ange resursgruppen och namnet på den virtuella datorn på följande sätt:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Följande exempelutdata visar att *autoUpgradeMinorVersion* har angetts till *true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifiera när en autoUpgradeMinorVersion inträffade

Om du vill se när en uppdatering av tillägget inträffade granskar du agentloggarna på den virtuella datorn *på /var/log/waagent.log*.

I exemplet nedan hade den virtuella datorn *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025* installerat. En snabbkorrigering var tillgänglig för *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027:*

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Agentbehörigheter

För att utföra sina uppgifter måste agenten köras som *roten*.

## <a name="troubleshoot-vm-extensions"></a>Felsöka VM-tillägg

Varje VM-tillägg kan ha felsökningssteg som är specifika för tillägget. När du till exempel använder tillägget För anpassat skript kan skriptkörningsinformation hittas lokalt på den virtuella dator där tillägget har körts. Eventuella tilläggsspecifika felsökningssteg beskrivs i tilläggsspecifik dokumentation.

Följande felsökningssteg gäller för alla VM-tillägg.

1. Om du vill kontrollera Linux-agentloggen tittar du på aktiviteten när tillägget etablerades i */var/log/waagent.log*

2. Kontrollera de faktiska tilläggsloggarna för mer information i */var/log/azure/ \<extensionName>*

3. Mer information om felkoder, kända problem och så vidare finns i felsökningsavsnitten för tilläggsspecifik dokumentation.

3. Titta på systemloggarna. Sök efter andra åtgärder som kan ha stört tillägget, till exempel en långvarig installation av ett annat program som krävde exklusiv pakethanteringsåtkomst.

### <a name="common-reasons-for-extension-failures"></a>Vanliga orsaker till tilläggsfel

1. Tillägg har 20 minuter att köra (undantag är CustomScript-tillägg, Chef och DSC som har 90 minuter). Om distributionen överskrider den här tiden markeras den som en tidsgräns. Orsaken till detta kan bero på låga resurs-VM:ar, andra VM-konfigurationer/startuppgifter som förbrukar stora mängder resurser medan tillägget försöker etableras.

2. Minimikraven är inte uppfyllda. Vissa tillägg har beroenden på VM-SKU:er, till exempel HPC-avbildningar. Tillägg kan kräva vissa krav på nätverksåtkomst, till exempel kommunikation till Azure Storage eller offentliga tjänster. Andra exempel kan vara åtkomst till paketdatabaser, slut på diskutrymme eller säkerhetsbegränsningar.

3. Exklusiv pakethanteringsåtkomst. I vissa fall kan du stöta på en långvarig VM-konfiguration och tilläggsinstallation som är i konflikt med varandra, där båda behöver exklusiv åtkomst till pakethanteraren.

### <a name="view-extension-status"></a>Visa tilläggsstatus

När ett VM-tillägg har körts mot en virtuell dator använder du [az vm get-instance-view för](/cli/azure/vm#az_vm_get_instance_view) att returnera tilläggets status enligt följande:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Utdata liknar följande exempelutdata:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

Status för tilläggskörning finns också i Azure Portal. Om du vill visa status för ett tillägg väljer du den virtuella datorn, **tillägg** och sedan önskat tillägg.

### <a name="rerun-a-vm-extension"></a>Köra ett VM-tillägg igen

Det kan finnas fall där ett VM-tillägg måste köras igen. Du kan köra ett tillägg igen genom att ta bort det och sedan köra tillägget igen med valfri körningsmetod. Om du vill ta bort ett tillägg använder [du az vm extension delete](/cli/azure/vm/extension#az_vm_extension_delete) på följande sätt:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Du kan också ta bort ett tillägg i Azure Portal på följande sätt:

1. Välj en virtuell dator.
2. Välj **Tillägg.**
3. Välj önskat tillägg.
4. Välj **Avinstallera.**

## <a name="common-vm-extension-reference"></a>Referens för gemensamt VM-tillägg

| Tilläggsnamn | Description | Mer information |
| --- | --- | --- |
| Anpassat skripttillägg för Linux |Köra skript mot en virtuell Azure-dator |[Anpassat skripttillägg för Linux](custom-script-linux.md) |
| VM Access-tillägg |Återfå åtkomst till en virtuell Azure-dator |[VM Access-tillägg](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics-tillägg |Hantera Azure Diagnostics |[Azure Diagnostics-tillägg](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Åtkomsttillägg för virtuell Azure-dator |Hantera användare och autentiseringsuppgifter |[VM-åtkomsttillägg för Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Nästa steg

Mer information om VM-tillägg finns i Översikt över tillägg och funktioner [för virtuella Azure-datorer.](overview.md)