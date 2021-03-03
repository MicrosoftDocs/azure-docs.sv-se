---
title: Azure VM-tillägg och-funktioner för Linux
description: Lär dig vilka tillägg som är tillgängliga för virtuella Azure-datorer i Linux, grupperade efter vad de erbjuder eller förbättrar.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.openlocfilehash: 66687f5954894d962a67767669081de15a30c7c3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672612"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Tillägg och funktioner för virtuella datorer för Linux

Azure-tillägg för virtuella datorer är små program som ger konfigurations- och automationsuppgifter på virtuella Azure-datorer efter distribution. Om en virtuell dator till exempel behöver programvaruinstallation, antivirusskydd eller körning av ett skript på den kan ett VM-tillägg användas. Azure VM-tillägg kan köras med Azure CLI, PowerShell, Azure Resource Manager-mallar och Azure-portalen. Tillägg kan paketeras med en ny VM-distribution eller köras mot valfritt befintligt system.

Den här artikeln innehåller en översikt över VM-tillägg, krav för att använda Azure VM-tillägg och rikt linjer för hur du identifierar, hanterar och tar bort VM-tillägg. Den här artikeln innehåller generaliserad information eftersom många VM-tillägg är tillgängliga, var och en med en potentiellt unik konfiguration. Tilläggs information finns i varje dokument som är specifikt för det enskilda tillägget.

## <a name="use-cases-and-samples"></a>Användnings fall och exempel

Flera olika Azure VM-tillägg är tillgängliga, var och en med ett särskilt användnings fall. Några exempel är:

- Använd PowerShell Desired State Configurations på en virtuell dator med DSC-tillägget för Linux. Mer information finns i [tillägget Azure Desired State Configuration](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurera övervakning av en virtuell dator med det virtuella dator tillägget för Microsoft Monitoring Agent. Mer information finns i [så här övervakar du en virtuell Linux-dator](/previous-versions/azure/virtual-machines/linux/tutorial-monitor).
- Konfigurera övervakning av din Azure-infrastruktur med chefs-eller Datadog-tillägget. Mer information finns i [chefs dokumenten](https://docs.chef.io/azure_portal.html) eller [Datadog-bloggen](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Förutom process-/regionsspecifika tillägg är ett anpassat skript tillägg tillgängligt för virtuella Windows-och Linux-datorer. Med tillägget för anpassat skript för Linux kan alla bash-skript köras på en virtuell dator. Anpassade skript är användbara för att utforma Azure-distributioner som kräver konfiguration utöver vad interna Azure-verktyg kan tillhandahålla. Mer information finns i avsnittet om [anpassat skript tillägg för Linux-datorer](custom-script-linux.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill hantera tillägget på den virtuella datorn behöver du Azure Linux-agenten installerad. Vissa enskilda tillägg har krav, till exempel åtkomst till resurser eller beroenden.

### <a name="azure-vm-agent"></a>Virtuell Azure-datoragent

Azure VM-agenten hanterar interaktioner mellan en virtuell Azure-dator och Azure Fabric-styrenheten. VM-agenten ansvarar för många funktionella aspekter av att distribuera och hantera virtuella Azure-datorer, inklusive att köra VM-tillägg. Azure VM-agenten är förinstallerad på Azure Marketplace-avbildningar och kan installeras manuellt på operativ system som stöds. Azure VM-agenten för Linux kallas Linux-agenten.

Information om operativ system som stöds och Installationsinstruktioner finns i [Azure Virtual Machine agent](agent-linux.md).

#### <a name="supported-agent-versions"></a>Agent versioner som stöds

För att kunna tillhandahålla bästa möjliga upplevelse finns det minimala versioner av agenten. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Operativ system som stöds

Linux-agenten körs på flera operativ system, men tillägg ramverket har en gräns för operativ system som tillägg. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Vissa tillägg stöds inte för alla operativ system och genererar *felkod 51, OS som inte stöds*. Kontrol lera den enskilda tilläggs dokumentationen för support.

#### <a name="network-access"></a>Nätverksåtkomst

Tilläggs paket laddas ned från Azure Storage förlängnings lagrings plats, och överförings status för tillägg skickas till Azure Storage. Om du använder en version som [stöds](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) av agenterna behöver du inte tillåta åtkomst till Azure Storage i den virtuella dator regionen, som kan använda agenten för att omdirigera kommunikationen till Azure Fabric Controller för agent kommunikation. Om du har en version som inte stöds av agenten måste du tillåta utgående åtkomst till Azure Storage i den regionen från den virtuella datorn.

> [!IMPORTANT]
> Om du har blockerat åtkomst till *168.63.129.16* med hjälp av gäst brand väggen, kommer tilläggen att fungera oberoende av ovanstående.

Agenter kan bara användas för att hämta tilläggs paket och rapporterings status. Om ett tillägg till exempel måste ladda ned ett skript från GitHub (anpassat skript) eller behöver åtkomst till Azure Storage (Azure Backup), måste ytterligare brand Väggs-och nätverks säkerhets grupps portar öppnas. Olika tillägg har olika krav, eftersom de är program i sin egen rätt. För tillägg som kräver åtkomst till Azure Storage kan du tillåta åtkomst med hjälp av Azure NSG service-taggar för [lagring](../../virtual-network/network-security-groups-overview.md#service-tags).

För att omdirigera begär Anden om agent trafik har Linux-agenten stöd för proxy server. Stöd för den här proxyservern tillämpar dock inte tillägg. Du måste konfigurera varje enskilt tillägg så att det fungerar med en proxy.

## <a name="discover-vm-extensions"></a>Identifiera VM-tillägg

Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. Om du vill se en fullständig lista använder du [AZ VM Extension image List](/cli/azure/vm/extension/image#az-vm-extension-image-list). I följande exempel visas alla tillgängliga tillägg på platsen för *västkusten* :

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Kör VM-tillägg

VIRTUELLA Azure-tillägg körs på befintliga virtuella datorer, vilket är användbart när du behöver göra konfigurations ändringar eller återställa anslutningar på en redan distribuerad virtuell dator. VM-tillägg kan också paketeras med Azure Resource Manager mallar distributioner. Med hjälp av tillägg med Resource Manager-mallar kan virtuella Azure-datorer distribueras och konfigureras utan åtgärder efter distribution.

Följande metoder kan användas för att köra ett tillägg mot en befintlig virtuell dator.

### <a name="azure-cli"></a>Azure CLI

Azure VM-tillägg kan köras mot en befintlig virtuell dator med kommandot [AZ VM Extension set](/cli/azure/vm/extension#az-vm-extension-set) . I följande exempel körs det anpassade skript tillägget mot en virtuell dator med namnet *myVM* i en resurs grupp med namnet *myResourceGroup*. Ersätt exempel resurs gruppens namn, namnet på den virtuella datorn och skriptet som ska köras (https: \/ /RAW.githubusercontent.com/me/Project/Hello.sh) med din egen information. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

När tillägget fungerar som det ska, ser utdata ut ungefär som i följande exempel:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

VM-tillägg kan tillämpas på en befintlig virtuell dator via Azure Portal. Välj den virtuella datorn i portalen, Välj **tillägg** och välj sedan **Lägg till**. Välj det tillägg du vill använda i listan över tillgängliga tillägg och följ anvisningarna i guiden.

Följande bild visar installationen av det anpassade skript tillägget för Linux från Azure Portal:

![Installera anpassat skript tillägg](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till i en Azure Resource Manager mall och köras med mallen. När du distribuerar ett tillägg med en mall kan du skapa fullständigt konfigurerade Azure-distributioner. Följande JSON tas till exempel från en Resource Manager-mall som distribuerar en uppsättning belastningsutjämnade virtuella datorer och Azure SQL Database och sedan installerar ett .NET Core-program på varje virtuell dator. VM-tillägget tar hand om program varu installationen.

Mer information finns i fullständig [Resource Manager-mall](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

Mer information om hur du skapar Resource Manager-mallar finns i [redigera Azure Resource Manager mallar](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Säkra data för VM-tillägg

När du kör ett VM-tillägg kan det vara nödvändigt att inkludera känslig information som autentiseringsuppgifter, lagrings konto namn och åtkomst nycklar för lagrings kontot. Många VM-tillägg innehåller en skyddad konfiguration som krypterar data och bara dekrypterar den i den virtuella mål datorn. Varje tillägg har ett särskilt skyddat konfigurations schema och var och en beskrivs i den tilläggsbaserade dokumentationen.

I följande exempel visas en instans av det anpassade skript tillägget för Linux. Kommandot som ska köras innehåller en uppsättning autentiseringsuppgifter. I det här exemplet krypteras inte kommandot som ska köras:

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

Om du flyttar **kommandot för att köra** egenskapen till den **skyddade** konfigurationen skyddas körnings strängen, som du ser i följande exempel:

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

Agenterna och tilläggen delar samma uppdaterings funktion. Vissa uppdateringar kräver inga ytterligare brand Väggs regler.

När det finns en tillgänglig uppdatering installeras den bara på den virtuella datorn när tilläggen ändras och andra ändringar i VM-modellen, till exempel:

- Datadiskar
- Tillägg
- Behållare för startdiagnostik
- Gäst operativ system hemligheter
- Storlek på virtuell dator
- Nätverksprofil

Utgivare gör uppdateringar tillgängliga för regioner vid olika tidpunkter, så det är möjligt att du kan ha virtuella datorer i olika regioner i olika versioner.

#### <a name="agent-updates"></a>Agent uppdateringar

Linux VM-agenten innehåller *etablerings agent kod* och *tillägg hanterings kod*  i ett paket som inte kan skiljas åt. Du kan inaktivera *etablerings agenten* när du vill etablera i Azure med Cloud-init. Information om hur du gör detta finns i [använda Cloud-Init](../linux/using-cloud-init.md).

Versioner av agenterna som stöds kan använda automatiska uppdateringar. Den enda kod som kan uppdateras är *tilläggs hanterings koden*, inte etablerings koden. *Etablerings agent koden* körs en gång.

*Tilläggs hanterings koden* ansvarar för kommunikation med Azure-infrastrukturen och hanterar de åtgärder för VM-tillägg som installation, rapporterings status, uppdaterar enskilda tillägg och tar bort dem. Uppdateringar innehåller säkerhets korrigeringar, fel korrigeringar och förbättringar i *tilläggs hanterings koden*.

När agenten installeras skapas ett överordnat daemon. Den här överordnade processen sedan skapar en underordnad process som används för att hantera tillägg. Om det finns en tillgänglig uppdatering för agenten laddas den ned, den överordnade processen stoppas, den underordnade processen uppgraderas och sedan startas den om. Om det uppstår problem med uppdateringen återställs den överordnade processen tillbaka till den tidigare underordnade versionen.

Det går inte att uppdatera den överordnade processen automatiskt. Den överordnade kan bara uppdateras av en distribution-paket uppdatering.

Kontrol lera vilken version du kör genom att kontrol lera följande `waagent` :

```bash
waagent --version
```

Utdata ser ut ungefär så här:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

I föregående exempel utdata är den överordnade eller paket distribuerade versionen *WALinuxAgent-2.2.17*

Mål tillstånds agenten är den automatiska uppdaterings versionen.

Vi rekommenderar starkt att du alltid har automatisk uppdatering av agenten, [AutoUpdate. enabled = y](./update-linux-agent.md). Om du inte aktiverar det här alternativet måste du uppdatera agenten manuellt och inte få fel-och säkerhets korrigeringar.

#### <a name="extension-updates"></a>Tilläggs uppdateringar

När en tilläggs uppdatering är tillgänglig laddar Linux-agenten ned och uppgraderar tillägget. Automatiska tilläggs uppdateringar är antingen *mindre* eller *snabb korrigeringar*. Du kan välja om du vill inaktivera tillägg som är *mindre* uppdateringar när du etablerar tillägget. I följande exempel visas hur du automatiskt uppgraderar lägre versioner i en Resource Manager-mall med *aktiverat autoupgrademinorversion ": true"*:

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

För att få de senaste fel korrigeringarna för smärre versioner, rekommenderar vi att du alltid väljer automatisk uppdatering i dina tillägg-distributioner. Uppdateringar av snabb korrigeringar som innehåller säkerhets-eller nyckel fel korrigeringar kan inte avregistreras.

### <a name="how-to-identify-extension-updates"></a>Så här identifierar du tilläggs uppdateringar

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifiera om tillägget har angetts med aktiverat autoupgrademinorversion på en virtuell dator

Du kan se från VM-modellen om tillägget etablerades med ' aktiverat autoupgrademinorversion '. Om du vill kontrol lera det använder du [AZ VM show](/cli/azure/vm#az-vm-show) och anger resurs gruppen och VM-namnet enligt följande:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Följande exempel på utdata visar att *aktiverat autoupgrademinorversion* är inställt på *True*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifiera när ett aktiverat autoupgrademinorversion har inträffat

Om du vill se när en uppdatering av tillägget har inträffat granskar du agent loggarna på den virtuella datorn på */var/log/waagent.log*.

I exemplet nedan hade den virtuella datorn *Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9025* installerat. En snabb korrigering är tillgänglig för *Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9027*:

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

## <a name="agent-permissions"></a>Agent behörigheter

För att utföra dess uppgifter måste agenten köras som *rot*.

## <a name="troubleshoot-vm-extensions"></a>Felsöka VM-tillägg

Varje VM-tillägg kan ha fel söknings steg som är speciella för tillägget. Om du till exempel använder tillägget för anpassat skript kan du hitta skript körnings information lokalt på den virtuella dator där tillägget kördes. Eventuella tilläggs fel söknings steg beskrivs i den särskilda dokumentationen.

Följande fel söknings steg gäller för alla VM-tillägg.

1. Kontrol lera loggen för Linux-agenten genom att titta på aktiviteten när ditt tillägg har allokerats i */var/log/waagent.log*

2. Se de faktiska tilläggs loggarna för mer information *i \<extensionName> /var/log/Azure/*

3. Sök efter felkoder, kända problem osv. i tilläggs dokumentation fel söknings avsnitt.

3. Titta på system loggarna. Kontrol lera om det finns andra åtgärder som kan ha stör tillägget, till exempel en tids krävande installation av ett annat program som kräver exklusiv åtkomst till paket hanteraren.

### <a name="common-reasons-for-extension-failures"></a>Vanliga orsaker till tilläggs problem

1. Tilläggen har 20 minuter att köra (undantag är CustomScript-tillägg, chef och DSC som har 90 minuter). Om distributionen överskrider den här tiden markeras den som en tids gräns. Orsaken till detta kan bero på att det finns låg resurs för virtuella datorer, andra VM-konfigurationer/starta aktiviteter som förbrukar stora mängder resurser, medan tillägget försöker etablera.

2. Minimi kraven är inte uppfyllda. Vissa tillägg har beroenden på VM SKU: er, till exempel HPC-avbildningar. Tillägg kan kräva vissa krav på nätverks åtkomst, t. ex. kommunikation med Azure Storage eller offentliga tjänster. Andra exempel kan vara till gång till paket lagrings utrymmen, ta slut på disk utrymme eller säkerhets begränsningar.

3. Exklusiv åtkomst till paket hanteraren. I vissa fall kan det uppstå en tids krävande konfiguration av virtuella datorer och tillägg i konflikt, där de båda behöver exklusiv åtkomst till paket hanteraren.

### <a name="view-extension-status"></a>Visa tilläggs status

När ett VM-tillägg har körts mot en virtuell dator kan du använda [AZ VM get-instance-View](/cli/azure/vm#az-vm-get-instance-view) för att returnera tilläggets status enligt följande:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Utdata liknar följande exempel på utdata:

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

Du kan också hitta körnings status för tillägg i Azure Portal. Om du vill visa status för ett tillägg väljer du den virtuella datorn, väljer **tillägg** och väljer sedan önskat tillägg.

### <a name="rerun-a-vm-extension"></a>Kör ett VM-tillägg igen

Det kan finnas fall där ett VM-tillägg måste köras igen. Du kan köra ett tillägg igen genom att ta bort det och sedan köra tillägget igen med en körnings metod som du väljer. Om du vill ta bort ett tillägg använder du [AZ VM Extension Delete](/cli/azure/vm/extension#az-vm-extension-delete) enligt följande:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Du kan också ta bort ett tillägg i Azure Portal på följande sätt:

1. Välj en virtuell dator.
2. Välj **tillägg**.
3. Välj önskat tillägg.
4. Välj **Avinstallera**.

## <a name="common-vm-extension-reference"></a>Vanlig referens för VM-tillägg

| Tilläggs namn | Beskrivning | Mer information |
| --- | --- | --- |
| Anpassat skript tillägg för Linux |Kör skript mot en virtuell Azure-dator |[Anpassat skript tillägg för Linux](custom-script-linux.md) |
| VM Access-tillägg |Få åtkomst till en virtuell Azure-dator |[VM Access-tillägg](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics-tillägg |Hantera Azure-diagnostik |[Azure Diagnostics-tillägg](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Tillägg för Azure VM Access |Hantera användare och autentiseringsuppgifter |[Åtkomst tillägg för virtuella datorer för Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Nästa steg

Mer information om tillägg för virtuella datorer finns i [Översikt över virtuella Azure-datorer och funktioner](overview.md).