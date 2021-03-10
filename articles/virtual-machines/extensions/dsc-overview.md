---
title: Önskad tillstånds konfiguration för Azure-översikt
description: Lär dig hur du använder den Microsoft Azure tilläggs hanteraren för PowerShell (Desired State Configuration). Artikeln innehåller krav, arkitektur och cmdlets.
services: virtual-machines
documentationcenter: ''
author: mgoedtel
manager: evansma
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 07/13/2020
ms.author: magoedte
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: dcdc325633aff5ab828cb1c82f4bb2d8becee967
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560046"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduktion till tilläggshanteraren för Azure Desired State Configuration

Azure VM-agenten och de associerade tilläggen ingår i Microsoft Azure infrastruktur tjänster. VM-tillägg är program varu komponenter som utökar VM-funktioner och fören klar olika hanterings åtgärder för virtuella datorer.

Det främsta användnings fallet för Azures tillägg för önskad tillstånds konfiguration (DSC) är att starta en virtuell dator till [tjänsten Azure Automation State Configuration (DSC)](../../automation/automation-dsc-overview.md).
Tjänsten ger [fördelar](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) som omfattar pågående hantering av VM-konfigurationen och integrering med andra operativa verktyg, till exempel Azure-övervakning.
Att använda tillägget för att registrera virtuella datorer till tjänsten är en flexibel lösning som även fungerar i Azure-prenumerationer.

Du kan använda DSC-tillägget oberoende av Automation DSC tjänsten.
Detta kommer dock bara att push-överföra en konfiguration till den virtuella datorn.
Ingen kontinuerlig rapportering är tillgänglig, förutom lokalt på den virtuella datorn.

Den här artikeln innehåller information om båda scenarierna: använda DSC-tillägget för automatisering onboarding och använda DSC-tillägget som ett verktyg för att tilldela konfigurationer till virtuella datorer med hjälp av Azure SDK.

## <a name="prerequisites"></a>Förutsättningar

- **Lokal dator**: om du vill interagera med tillägget för Azure VM måste du antingen använda Azure Portal eller Azure PowerShell SDK.
- **Gästa Gent**: den virtuella Azure-dator som konfigureras av DSC-konfigurationen måste vara ett operativ system som stöder Windows Management Framework (WMF) 4,0 eller senare. En fullständig lista över OS-versioner som stöds finns i [versions historik för DSC-tillägg](../../automation/automation-dsc-extension-history.md).

## <a name="terms-and-concepts"></a>Termer och begrepp

Den här guiden förutsätter att du är bekant med följande begrepp:

- **Konfiguration**: ett DSC-konfigurationsobjekt.
- **Node**: ett mål för en DSC-konfiguration. I det här dokumentet refererar *noden* alltid till en virtuell Azure-dator.
- **Konfigurations data**: en. psd1-fil med miljö data för en konfiguration.

## <a name="architecture"></a>Arkitektur

Azure DSC-tillägget använder Azure VM agent Framework för att leverera, införa och rapportera om DSC-konfigurationer som körs på virtuella Azure-datorer. DSC-tillägget accepterar ett konfigurations dokument och en uppsättning parametrar. Om ingen fil anges bäddas ett [standard konfigurations skript](#default-configuration-script) in med tillägget. Standard konfigurations skriptet används endast för att ange metadata i den [lokala Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

När tillägget anropas för första gången installeras en version av WMF med hjälp av följande logik:

- Om Azure VM OS är Windows Server 2016 vidtas ingen åtgärd. Windows Server 2016 har redan den senaste versionen av PowerShell installerad.
- Om **wmfVersion** -egenskapen har angetts installeras den versionen av WMF, om inte den versionen är inkompatibel med den virtuella datorns operativ system.
- Om ingen **wmfVersion** -egenskap anges installeras den senaste tillämpliga versionen av WMF.

Installation av WMF kräver en omstart. Efter omstarten laddar tillägget. zip-filen som anges i egenskapen **modulesUrl** , om den har angetts. Om den här platsen finns i Azure Blob Storage kan du ange en SAS-token i egenskapen **sasToken** för att komma åt filen. När. zip har hämtats och packats upp körs konfigurations funktionen som definieras i **configurationFunction** för att generera en MOF-fil ([Managed Object Format](/windows/win32/wmisdk/managed-object-format--mof-)). Tillägget körs sedan `Start-DscConfiguration -Force` med hjälp av den genererade MOF-filen. Tillägget fångar utdata och skriver den till Azures status kanal.

### <a name="default-configuration-script"></a>Standard konfigurations skript

Azure DSC-tillägget innehåller ett standard konfigurations skript som är avsett att användas när du registrerar en virtuell dator till tjänsten Azure Automation DSC. Skript parametrarna är justerade med de konfigurerbara egenskaperna för [lokal Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig). Skript parametrar finns i [standard konfigurations skript](dsc-template.md#default-configuration-script) i [önskat tillstånds konfigurations tillägg med Azure Resource Manager mallar](dsc-template.md). Det fullständiga skriptet finns i mallen för [Azure snabb start i GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Information om registrering med tjänsten Azure Automation State Configuration (DSC)

När du använder DSC-tillägget för att registrera en nod med tillstånds konfigurations tjänsten måste tre värden anges.

- RegistrationUrl – https-adressen för det Azure Automation kontot
- RegistrationKey – en delad hemlighet som används för att registrera noder med tjänsten
- NodeConfigurationName – namnet på den Node-konfiguration (MOF) som ska hämtas från tjänsten för att konfigurera Server rollen

Den här informationen kan visas i Azure Portal eller så kan du använda PowerShell.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

För konfigurations namnet för noden, se till att nodens konfiguration finns i konfiguration av Azure-tillstånd.  Om den inte gör det returnerar tilläggs distributionen ett haveri.  Kontrol lera också att du använder namnet på *nodens konfiguration* och inte konfigurationen.
En konfiguration definieras i ett skript som används [för att kompilera Node-konfigurationen (MOF-filen)](../../automation/automation-dsc-compile.md).
Namnet är alltid konfigurationen följt av en punkt `.` och antingen `localhost` eller ett särskilt dator namn.

## <a name="dsc-extension-in-resource-manager-templates"></a>DSC-tillägg i Resource Manager-mallar

I de flesta fall är distributions mallar för Resource Manager det förväntade sättet att arbeta med DSC-tillägget. Mer information och exempel på hur du inkluderar DSC-tillägget i distributions mallar för Resource Manager finns i [önskat tillstånds konfigurations tillägg med Azure Resource Manager mallar](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>PowerShell-cmdletar för DSC-tillägg

PowerShell-cmdletar som används för att hantera DSC-tillägget används bäst i interaktiva fel söknings-och informations insamlings scenarier. Du kan använda cmdlet: ar för att paketera, publicera och övervaka distributioner av DSC-tillägg. Cmdletar för DSC-tillägget har inte uppdaterats för att fungera med [standard konfigurations skriptet](#default-configuration-script).

Cmdleten **Publish-AzVMDscConfiguration** tar i en konfigurations fil, söker efter beroende DSC-resurser och skapar sedan en zip-fil. Zip-filen innehåller de konfigurations-och DSC-resurser som behövs för att införa konfigurationen. Cmdleten kan också skapa paketet lokalt med hjälp av parametern *-OutputArchivePath* . Annars publicerar cmdlet. zip-filen till Blob Storage och skyddar den sedan med en SAS-token.

Konfigurations skriptet. ps1 som cmdleten skapar finns i. zip-filen i roten i arkivmappen. Mappen module placeras i mappen Arkiv i resurser.

Cmdlet **: en Set-AzVMDscExtension** infogar de inställningar som PowerShell DSC-tillägget kräver i ett konfigurations objekt för virtuell dator.

Cmdlet: en **Get-AzVMDscExtension** hämtar DSC-tilläggets status för en angiven virtuell dator.

Cmdlet: en **Get-AzVMDscExtensionStatus** hämtar STATUSEN för DSC-konfigurationen som hanteras av DSC-tilläggs hanteraren. Den här åtgärden kan utföras på en enskild virtuell dator eller en grupp med virtuella datorer.

Cmdlet: en **Remove-AzVMDscExtension** tar bort tilläggs hanteraren från en angiven virtuell dator. Den här cmdleten tar *inte* bort konfigurationen, avinstallerar WMF eller ändrar de tillämpade inställningarna på den virtuella datorn. Den tar bara bort tilläggs hanteraren.

Viktig information om cmdlets för DSC-tillägg i Resource Manager:

- Azure Resource Manager-cmdlet: ar är synkrona.
- Parametrarna *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *version* och *location* krävs.
- *ArchiveResourceGroupName* är en valfri parameter. Du kan ange den här parametern när ditt lagrings konto tillhör en annan resurs grupp än den där den virtuella datorn skapas.
- Använd den automatiska **uppdaterings** växeln för att automatiskt uppdatera tilläggs hanteraren till den senaste versionen när den är tillgänglig. Den här parametern har möjlighet att starta om den virtuella datorn när en ny version av WMF lanseras.

### <a name="get-started-with-cmdlets"></a>Kom igång med cmdletar

Azure DSC-tillägget kan använda DSC-konfigurationsobjekt för att konfigurera virtuella Azure-datorer direkt under distributionen. Det här steget registrerar inte noden som ska automatiseras. Noden hanteras *inte* centralt.

I följande exempel visas ett enkelt exempel på en konfiguration. Spara konfigurationen lokalt som iisInstall.ps1.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

Följande kommandon placerar iisInstall.ps1-skriptet på den angivna virtuella datorn. Kommandona kör också konfigurationen och rapporterar sedan om status.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera DSC-tillägget till en befintlig virtuell dator.

För en virtuell dator som kör Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

För en virtuell dator som kör Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Azure Portal funktioner

Så här konfigurerar du DSC i portalen:

1. Gå till en virtuell dator.
2. Under **Inställningar** väljer du **Tillägg**.
3. På den nya sidan som skapas väljer du **+ Lägg till** och väljer sedan **PowerShell Desired State Configuration**.
4. Klicka på **skapa** längst ned på sidan tilläggs information.

Portalen samlar in följande ingångar:

- **Konfigurations moduler eller skript**: det här fältet är obligatoriskt (formuläret har inte uppdaterats för [standard konfigurations skriptet](#default-configuration-script)). Konfigurations moduler och-skript kräver en. ps1-fil som har ett konfigurations skript eller en. zip-fil med konfigurations skriptet. ps1 på roten. Om du använder en. zip-fil måste alla beroende resurser tas med i modulens mappar i. zip-filen. Du kan skapa. zip-filen med hjälp av cmdleten **Publish-AzureVMDscConfiguration-OutputArchivePath** som ingår i Azure PowerShell SDK. Zip-filen överförs till din användar-blob-lagring och skyddas av en SAS-token.

- **Konfiguration av modulens kvalificerade namn**: du kan inkludera flera konfigurations funktioner i en. ps1-fil. Ange namnet på Configuration. ps1-skriptet följt av \\ och namnet på konfigurations funktionen. Om t. ex. ps1-skriptet har namnet configuration.ps1 och konfigurationen är **skriptet iisinstall**, anger **configuration.ps1 \iisinstall**.

- **Konfigurations argument**: om konfigurations funktionen tar argument anger du dem här i formatet **argumentName1 = värde1, argumentName2 = värde2**. Det här formatet är ett annat format där konfigurations argument godkänns i PowerShell-cmdletar eller Resource Manager-mallar.

- **PSD1-fil för konfigurations data**: om konfigurationen kräver en konfigurations data fil i `.psd1` använder du det här fältet för att välja data filen och ladda upp den till blob-lagringen för användare. Konfigurations data filen skyddas av en SAS-token i Blob Storage.

- **WMF-version**: anger den version av Windows Management Framework (WMF) som ska installeras på den virtuella datorn. Om du anger den här egenskapen till senaste installeras den senaste versionen av WMF. För närvarande är de enda möjliga värdena för den här egenskapen 4,0, 5,0, 5,1 och senaste. Dessa möjliga värden är beroende av uppdateringar. Standardvärdet är **senaste**.

- **Data insamling**: anger om tillägget ska samla in telemetri. Mer information finns i [data insamling för Azure DSC-tillägg](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).

- **Version**: anger vilken version av DSC-tillägget som ska installeras. Information om versioner finns i [versions historik för DSC-tillägg](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- **Del version för automatisk uppgradering**: det här fältet mappar till **AutoUpdate** -växeln i-cmdletarna och aktiverar tillägget till att uppdateras automatiskt till den senaste versionen under installationen. **Ja** instruerar tilläggs hanteraren att använda den senaste tillgängliga versionen och **ingen** kommer att tvinga den angivna **versionen** att installeras. Att välja  nej eller **Nej** är inte detsamma som att välja **Nej**.

## <a name="logs"></a>Loggar

Loggar för tillägget lagras på följande plats: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Nästa steg

- Mer information om PowerShell DSC finns i [PowerShell-dokumentations centret](/powershell/scripting/dsc/overview/overview).
- Granska [Resource Manager-mallen för DSC-tillägget](dsc-template.md).
- Om du vill ha fler funktioner som du kan hantera med hjälp av PowerShell DSC kan du bläddra i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)för fler DSC-resurser.
- Mer information om hur du skickar känsliga parametrar till konfigurationer finns i [Hantera autentiseringsuppgifter säkert med DSC-tilläggs hanteraren](dsc-credentials.md).
