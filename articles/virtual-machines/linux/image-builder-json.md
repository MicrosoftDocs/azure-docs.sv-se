---
title: Skapa en Azure Image Builder-mall (förhands granskning)
description: Lär dig hur du skapar en mall som ska användas med Azure Image Builder.
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: aaaabe758b036335062907c8e5549ae876c63997
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594741"
---
# <a name="preview-create-an-azure-image-builder-template"></a>För hands version: skapa en Azure Image Builder-mall 

Azure Image Builder använder en. JSON-fil för att skicka information till Image Builder-tjänsten. I den här artikeln går vi igenom avsnitten i JSON-filen så att du kan bygga egna. Se exempel på fullständiga. JSON-filer i [Azure Image Builder-GitHub](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts).

Detta är det grundläggande mallformat:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Typ-och API-version

`type`Är resurs typen, som måste vara `"Microsoft.VirtualMachineImages/imageTemplates"` . `apiVersion`Kommer att ändras med tiden som API-ändringar, men bör vara `"2020-02-14"` för hands version.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Location

Platsen är den region där den anpassade avbildningen kommer att skapas. För för hands versionen av Image Builder stöds följande regioner:

- East US
- USA, östra 2
- USA, västra centrala
- USA, västra
- USA, västra 2
- Europa, norra
- Europa, västra


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Som standard använder Image Builder en "Standard_D1_v2"-version av den virtuella datorn. du kan åsidosätta detta, om du till exempel vill anpassa en avbildning för en GPU-VM behöver du en GPU VM-storlek. Detta är valfritt.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Som standard kommer bild verktyget inte att ändra bildens storlek, den kommer att använda storleken från käll bilden. Du kan **bara** öka storleken på operativ system disken (Win och Linux), detta är valfritt och värdet 0 innebär att du lämnar samma storlek som käll bilden. Du kan inte minska storleken på OS-disken till mindre än storleken från käll bilden.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Om du inte anger några VNET-egenskaper skapas en egen VNET-, offentlig IP-och NSG-avbildnings verktyg. Den offentliga IP-adressen används för tjänsten för att kommunicera med den virtuella datorn, men om du inte vill att en offentlig IP-adress ska ha åtkomst till dina befintliga VNET-resurser, till exempel konfigurations servrar (DSC, chef, Puppet, Ansible), fil resurser osv., kan du ange ett VNET. Mer information finns i dokumentationen för [nätverket](image-builder-networking.md), detta är valfritt.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Taggar

Detta är nyckel/värde-par som du kan ange för den bild som genereras.

## <a name="depends-on-optional"></a>Är beroende av (valfritt)

Det här valfria avsnittet kan användas för att säkerställa att beroenden har slutförts innan du fortsätter. 

```json
    "dependsOn": [],
```

Mer information finns i [definiera resurs beroenden](../../azure-resource-manager/templates/define-resource-dependency.md#dependson).

## <a name="identity"></a>Identitet

Krävs – för att Image Builder ska ha behörighet att läsa/skriva bilder kan du läsa in skript från Azure Storage du måste skapa en Azure User-Assigned-identitet som har behörighet till de enskilda resurserna. Mer information om hur Image Builder-behörigheter fungerar och relevanta steg finns i [dokumentationen](image-builder-user-assigned-identity.md).


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Stöd för avbildnings-Builder för en User-Assigned identitet:
* Har endast stöd för en enda identitet
* Stöder inte anpassade domän namn

Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md).
Mer information om hur du distribuerar den här funktionen finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity).

## <a name="properties-source"></a>Egenskaper: källa

`source`Avsnittet innehåller information om käll avbildningen som ska användas av Image Builder. Image Builder har för närvarande endast stöd för att skapa Hyper-V generation (gen1) 1-avbildningar i Azure-galleriet för delad avbildning (SIG) eller hanterad avbildning. Om du vill skapa Gen2-avbildningar måste du använda en käll Gen2 avbildning och distribuera till VHD. Därefter måste du skapa en hanterad avbildning från den virtuella hård disken och mata in den i SIG som en Gen2-avbildning.

API: t kräver en ' SourceType ' som definierar källan för avbildnings versionen, för närvarande finns det tre typer:
- PlatformImage – anger att käll avbildningen är en Marketplace-avbildning.
- ManagedImage – Använd det här när du startar från en vanlig hanterad avbildning.
- SharedImageVersion – används när du använder en avbildnings version i ett delat avbildnings galleri som källa.


> [!NOTE]
> När du använder befintliga anpassade Windows-avbildningar kan du köra Sysprep-kommandot upp till 8 gånger på en enda Windows-avbildning. mer information finns i [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) -dokumentationen.

### <a name="platformimage-source"></a>PlatformImage-källa 
Azure Image Builder stöder Windows Server och klient, och Linux Azure Marketplace-avbildningar finns [här](../image-builder-overview.md#os-support) för den fullständiga listan. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Egenskaperna här är samma som används för att skapa virtuella datorer med hjälp av AZ CLI, och kör följande för att hämta egenskaperna: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Du kan använda "senaste" i versionen om versionen utvärderas när avbildningen skapas, inte när mallen skickas. Om du använder den här funktionen med det delade avbildnings galleriet kan du undvika att skicka mallen igen och köra avbildningen igen med intervall, så att dina avbildningar återskapas från de senaste bilderna.

#### <a name="support-for-market-place-plan-information"></a>Stöd för information om marknads plats plan
Du kan också ange plan information, till exempel:
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>ManagedImage-källa

Anger käll avbildningen som en befintlig hanterad avbildning av en generaliserad virtuell hård disk eller virtuell dator.

> [!NOTE]
> Den hanterade avbildningen måste vara av ett operativ system som stöds och avbildningen måste ha samma region som din Azure Image Builder-mall. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`Ska vara den hanterade avbildningens ResourceID. Använd `az image list` för att visa en lista över tillgängliga avbildningar.


### <a name="sharedimageversion-source"></a>SharedImageVersion-källa
Anger käll avbildningen av en befintlig avbildnings version i ett galleri för delade avbildningar.

> [!NOTE]
> Den hanterade avbildningen måste vara av ett operativ system som stöds och avbildningen måste vara samma region som din Azure Image Builder-mall, om inte, ska du replikera avbildnings versionen till mallen för Image Builder-mallen.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`Ska vara avbildnings versionens ResourceID. Använd [AZ sig-avbildning – versions lista](/cli/azure/sig/image-version#az-sig-image-version-list) för att lista avbildnings versioner.


## <a name="properties-buildtimeoutinminutes"></a>Egenskaper: buildTimeoutInMinutes

Som standard körs Image Builder i 240 minuter. Efter det kommer timeout och stoppas, oavsett om avbildningen är slutförd eller inte. Om tids gränsen har nåtts visas ett fel som liknar detta:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Om du inte anger något värde för buildTimeoutInMinutes, eller om du anger värdet 0, används standardvärdet. Du kan öka eller minska värdet, upp till maximalt 960mins (16hrs). För Windows rekommenderar vi inte att du anger det här under 60 minuter. Om du upptäcker att du når tids gränsen granskar du [loggarna](image-builder-troubleshoot.md#customization-log)för att se om anpassnings steget väntar på något som indata från användaren. 

Om du upptäcker att du behöver mer tid för att anpassningarna ska slutföras, ställer du in den på det du tycker att du behöver, med lite extra kostnad. Men Ställ inte in den för hög eftersom du kanske måste vänta tills den är tids gräns innan du ser ett fel. 


## <a name="properties-customize"></a>Egenskaper: anpassa

Avbildnings verktyget stöder flera "anpassningar". Anpassningar är funktioner som används för att anpassa din avbildning, till exempel köra skript eller starta om servrar. 

När du använder `customize` : 
- Du kan använda flera anpassningar, men de måste ha ett unikt `name` .
- Anpassningar körs i den ordning som anges i mallen.
- Om en anpassning Miss lyckas, kommer hela anpassnings komponenten att Miss lyckas och rapportera ett fel.
- Det rekommenderas starkt att du testar skriptet noggrant innan du använder det i en mall. Det blir enklare att felsöka skriptet på din egen virtuella dator.
- Lägg inte till känsliga data i skripten. 
- Skript platserna måste vara offentligt tillgängliga, såvida du inte använder [MSI](./image-builder-user-assigned-identity.md).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
Avsnittet Anpassa är en matris. Azure Image Builder kommer att köras via anpassningarna i nummerordning. Eventuella misslyckanden i en anpassnings process kommer inte att kunna skapas. 

> [!NOTE]
> Infogade kommandon kan visas i definition av avbildnings mal len och Microsoft Support när de hjälper till med ett support ärende. Om du har känslig information bör den flyttas till skript i Azure Storage, där åtkomst kräver autentisering.
 
### <a name="shell-customizer"></a>Shell-anpassning

Shell-anpassningen stöder körning av gränssnitts skript, de måste vara offentligt tillgängliga för IB för att få åtkomst till dem.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

OS-support: Linux 
 
Anpassa egenskaper:

- **typ** – gränssnitt 
- **namn** – namn för att spåra anpassningen 
- **scriptUri** -URI till filens plats 
- **infogad** matris av Shell-kommandon, avgränsade med kommatecken.
- **sha256Checksum** -värdet för filens SHA256-kontrollsumma, du genererar det här lokalt och sedan kontrol leras kontroll summor och Image Builder.
    * Skapa sha256Checksum med hjälp av en terminal på Mac/Linux-körning: `sha256sum <fileName>`

> [!NOTE]
> Infogade kommandon lagras som en del av definitionen för avbildnings mal len. du kan se dessa när du dumpar avbildnings definitionen, och dessa visas också för Microsoft Support i händelse av ett support ärende i fel söknings syfte. Om du har känsliga kommandon eller värden rekommenderar vi att de flyttas till skript och använder en användar identitet för att autentisera till Azure Storage.

#### <a name="super-user-privileges"></a>Behörigheter för superanvändare
För kommandon som ska köras med superuser-privilegier måste de föregås av `sudo` , du kan lägga till dem i skript eller använda den infogade kommandona, till exempel:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
Exempel på ett skript som använder sudo som du kan referera till med scriptUri:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Starta om anpassning av Windows 
Med alternativet starta om anpassning kan du starta om en virtuell Windows-dator och vänta tills den är online igen, så att du kan installera program vara som kräver en omstart.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

OS-support: Windows
 
Anpassa egenskaper:
- **Typ**: WindowsRestart
- **restartCommand** -kommando för att köra omstarten (valfritt). Standardvärdet är `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – kommando för att kontrol lera om omstart lyckades (valfritt). 
- **restartTimeout** – timeout för omstart har angetts som en sträng med storlek och enhet. Till exempel `5m` (5 minuter) eller `2h` (2 timmar). Standardvärdet är: ' 5 m '

### <a name="linux-restart"></a>Linux-omstart  
Det finns ingen Linux restart-anpassning, men om du installerar driv rutiner eller komponenter som kräver en omstart, kan du installera dem och anropa en omstart med hjälp av Shell-anpassningen, det finns en 20min SSH-tidsgräns till den virtuella dator som skapas.

### <a name="powershell-customizer"></a>PowerShell-anpassning 
Shell-anpassningen stöder körning av PowerShell-skript och infogat kommando, skripten måste vara offentligt tillgängliga för IB för att få åtkomst till dem.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

OS-support: Windows och Linux

Anpassa egenskaper:

- **typ** – PowerShell.
- **scriptUri** -URI till platsen för PowerShell-skriptfilen. 
- **infogade** – infogade kommandon som ska köras, avgränsade med kommatecken.
- **validExitCodes** – valfria, giltiga koder som kan returneras från skriptet/inline-kommandot. Detta undviker att det rapporteras ett skript/inline-kommando.
- **runElevated** – valfritt, booleskt, stöd för att köra kommandon och skript med förhöjd behörighet.
- **sha256Checksum** -värdet för filens SHA256-kontrollsumma, du genererar det här lokalt och sedan kontrol leras kontroll summor och Image Builder.
    * Skapa sha256Checksum med hjälp av PowerShell på Windows [Get-hash](/powershell/module/microsoft.powershell.utility/get-filehash)


### <a name="file-customizer"></a>Fil anpassning

Med fil anpassnings verktyget kan Image Builder Ladda ned en fil från en GitHub eller Azure Storage. Om du har en pipeline för avbildnings utveckling som förlitar sig på bygg artefakter kan du sedan konfigurera fil anpassningen så att den laddas ned från build-resursen och flytta artefakterna till avbildningen.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

OS-support: Linux och Windows 

Fil anpassnings egenskaper:

- **sourceUri** – en tillgänglig lagrings slut punkt kan vara GitHub eller Azure Storage. Du kan bara ladda ned en fil, inte en hel katalog. Om du behöver hämta en katalog använder du en komprimerad fil och expanderar den sedan med Shell-eller PowerShell-anpassningarna. 

> [!NOTE]
> Om sourceUri är ett Azure Storage konto, oavsett om blobben har marker ATS som offentlig, så kommer du att ge den hanterade användarens identitet behörighet att läsa åtkomst till bloben. Se det här [exemplet](./image-builder-user-assigned-identity.md#create-a-resource-group) för att ange lagrings behörigheter.

- **destination** – detta är den fullständiga sökvägen till målet och fil namnet. Alla refererade sökvägar och under kataloger måste finnas, Använd Shell-eller PowerShell-anpassningarna för att ställa in dem på förhand. Du kan använda skript anpassningarna för att skapa sökvägen. 

Detta stöds av Windows-kataloger och Linux-sökvägar, men det finns vissa skillnader: 
- Linux OS – det enda Sök vägs avbildnings verktyget kan skriva till är/tmp.
- Windows – ingen Sök vägs begränsning, men sökvägen måste finnas.
 
 
Om det uppstår ett fel vid försök att hämta filen, eller om den placeras i en angiven katalog, kommer anpassnings steget inte att fungera, och detta görs i anpassnings loggen.

> [!NOTE]
> Fil anpassningen är bara lämplig för små fil hämtningar, < 20 MB. För större fil hämtningar används ett skript eller ett infogat kommando, koden används för att ladda ned filer, till exempel Linux `wget` eller `curl` Windows, `Invoke-WebRequest` .

### <a name="windows-update-customizer"></a>Windows Update anpassning
Den här anpassningen bygger på [community Windows Update-etableringen](https://packer.io/docs/provisioners/community-supported.html) för Packer, som är ett projekt med öppen källkod som underhålls av communityn för programpaket. Microsoft testar och validerar etableringen med tjänsten Image Builder och har stöd för att undersöka problem med den, och det kan också fungera att lösa problem, men projektet med öppen källkod stöds inte av Microsoft. Detaljerad dokumentation om och hjälp med Windows Update etablerings tjänsten finns i projektets lagrings plats.

```json
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS support: Windows
```

Anpassa egenskaper:
- **typ**  – windowsupdate.
- **searchCriteria** – valfritt, definierar vilken typ av uppdateringar som installeras (rekommenderas, viktigt osv.), BrowseOnly = 0 och IsInstalled = 0 (rekommenderas) är standardvärdet.
- **filter** – valfritt, gör att du kan ange ett filter för att ta med eller undanta uppdateringar.
- **updateLimit** – valfritt, definierar hur många uppdateringar som kan installeras, standard 1000.
 
> [!NOTE]
> Windows Update anpassning kan Miss lyckas om det finns väntande Windows-omstarter eller om program installationer fortfarande körs, så det kan hända att du ofta ser det här felet i anpassningen. log `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` . Vi rekommenderar starkt att du funderar på att lägga till i en Windows-omstart och/eller att göra det möjligt för program att slutföra sina installationer med [vila](/powershell/module/microsoft.powershell.utility/start-sleep) -eller wait-kommandon i infogade kommandon eller skript innan du kör Windows Update.

### <a name="generalize"></a>Generalisera 
Som standard kommer Azure Image Builder också att köra "avetablering"-kod i slutet av varje bild anpassnings fas till "generalize"-avbildningen. Att generalisera är en process där avbildningen konfigureras så att den kan återanvändas för att skapa flera virtuella datorer. För virtuella Windows-datorer använder Azure Image Builder Sysprep. För Linux kör Azure Image Builder "waagent-deetablering". 

Kommandona Image Builder-användare att generalisera är kanske inte lämpliga för varje situation, så Azure Image Builder gör det möjligt att anpassa kommandot om det behövs. 

Om du migrerar befintlig anpassning och använder olika Sysprep/waagent-kommandon kan du använda de allmänna kommandona för Image Builder och om det inte går att skapa en virtuell dator använder du dina egna Sysprep-eller waagent-kommandon.

Om Azure Image Builder skapar en anpassad Windows-avbildning och du skapar en virtuell dator från den, kontrollerar du att den virtuella datorn Miss lyckas eller inte slutförs utan problem, du måste gå igenom Windows Server Sysprep-dokumentationen eller utlösa en supportbegäran med support teamet för Windows Server Sysprep, som kan felsöka och Avisera om korrekt Sysprep-användning.


#### <a name="default-sysprep-command"></a>Standard kommando för Sysprep
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>Standard kommando för att avetablera Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Åsidosätta kommandona
Om du vill åsidosätta kommandona använder du PowerShell-eller Shell-skript provisioor för att skapa kommando filerna med det exakta fil namnet och lägga dem i rätt kataloger:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/DeprovisioningScript.sh

Image Builder läser dessa kommandon, de skrivs ut till AIB-loggarna, anpassning. log. Se [fel sökning](image-builder-troubleshoot.md#customization-log) för insamling av loggar.
 
## <a name="properties-distribute"></a>Egenskaper: Distribuera

Azure Image Builder stöder tre distributions mål: 

- **managedImage** -hanterad avbildning.
- **sharedImage** -delat avbildnings Galleri.
- **VHD** -VHD i ett lagrings konto.

Du kan distribuera en avbildning till båda mål typerna i samma konfiguration.

> [!NOTE]
> Standardvärdet för AIB Sysprep inkluderar inte "/läge: VM", men detta kan vara nödvändigt när du skapar avbildningar som ska ha HyperV-rollen installerad. Om du behöver lägga till det här kommando argumentet måste du åsidosätta Sysprep-kommandot.

Eftersom du kan ha fler än ett mål att distribuera till, har Image Builder ett tillstånd för varje distributions mål som kan nås genom att fråga `runOutputName` .  `runOutputName`Är ett objekt som du kan skicka frågor till efter distribution för information om distributionen. Du kan till exempel fråga platsen för den virtuella hård disken eller regioner där avbildnings versionen har repliker ATS till, eller SIG-avbildnings version som skapats. Detta är en egenskap för varje distributions mål. `runOutputName`Måste vara unik för varje distributions mål. Här är ett exempel som frågar en distribution av delade avbildnings Galleri:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

Utdata:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribuera: managedImage

Avbildningens utdata är en hanterad avbildnings resurs.

```json
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
```
 
Distribuera egenskaper:
- **typ** – managedImage 
- **imageId** – resurs-ID för mål avbildningen, förväntat format:/Subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **plats** – plats för den hanterade avbildningen.  
- **runOutputName** – unikt namn för identifiering av distributionen.  
- **artifactTags** – valfri användardefinierad nyckel värde par taggar.
 
 
> [!NOTE]
> Mål resurs gruppen måste finnas.
> Om du vill att avbildningen ska distribueras till en annan region ökar distributions tiden. 

### <a name="distribute-sharedimage"></a>Distribuera: sharedImage 
Azures delade avbildnings galleri är en ny avbildnings hanterings tjänst som gör det möjligt att hantera replikering av avbildnings regioner, versions hantering och delning av anpassade avbildningar. Azure Image Builder stöder distribution med den här tjänsten, så du kan distribuera avbildningar till regioner som stöds av delade bild gallerier. 
 
Ett delat avbildnings Galleri består av: 
 
- Galleri-behållare för flera delade avbildningar. Ett galleri har distribuerats i en region.
- Bild definitioner – en konceptuell gruppering för bilder. 
- Avbildnings versioner – det här är en avbildnings typ som används för att distribuera en virtuell dator eller skalnings uppsättning. Avbildnings versioner kan replikeras till andra regioner där de virtuella datorerna måste distribueras.
 
Innan du kan distribuera till avbildnings galleriet måste du skapa ett galleri och en bild definition, se [delade avbildningar](../shared-images-cli.md). 

```json
{
    "type": "SharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Distribuera egenskaper för delade avbildnings gallerier:

- **typ** -sharedImage  
- **galleryImageId** – ID för galleriet för delad avbildning, detta kan anges i två format:
    * Automatisk versions hantering – Image Builder genererar ett högfärgat versions nummer. det här är användbart när du vill fortsätta att bygga om avbildningar från samma mall: formatet är: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Explicit versions hantering – du kan skicka in det versions nummer som du vill att Image Builder ska använda. Formatet är: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** – unikt namn för identifiering av distributionen.  
- **artifactTags** – valfri användardefinierad nyckel värde par taggar.
- **replicationRegions** -matris för replikering. En av regionerna måste vara den region där galleriet har distribuerats. Att lägga till regioner innebär en ökning av bygg tiden, eftersom versionen inte slutförs förrän replikeringen har slutförts.
- **excludeFromLatest** (valfritt) Detta gör att du kan markera den avbildnings version som du skapar inte som den senaste versionen i sig-definitionen. standardvärdet är "false".
- **storageAccountType** (valfritt) AIB har stöd för att ange dessa typer av lagring för avbildnings versionen som ska skapas:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Om avbildnings mal len och den refererade `image definition` inte finns på samma plats visas ytterligare tid för att skapa bilder. Image Builder har för närvarande ingen `location` parameter för avbildnings versions resursen, vi tar den från den överordnade `image definition` . Om en bild definition till exempel är i väst och du vill att avbildnings versionen ska replikeras till öster, kopieras en blob till väst, från detta skapas en avbildnings versions resurs i väst, och replikeras sedan till öster. Se till att `image definition` mallen och är på samma plats för att undvika den ytterligare replikerings tiden.


### <a name="distribute-vhd"></a>Distribuera: virtuell hård disk  
Du kan skriva utdata till en virtuell hård disk. Du kan sedan kopiera den virtuella hård disken och använda den för att publicera på Azure MarketPlace eller använda med Azure Stack.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
OS-support: Windows och Linux

Distribuera VHD-parametrar:

- **typ** -VHD.
- **runOutputName** – unikt namn för identifiering av distributionen.  
- **taggar** – valfritt användardefinierat nyckel värde par.
 
Azure Image Builder tillåter inte användaren att ange en lagrings konto plats, men du kan fråga efter status för `runOutputs` att hämta platsen.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> När den virtuella hård disken har skapats kopierar du den till en annan plats så snart som möjligt. Den virtuella hård disken lagras i ett lagrings konto i den tillfälliga resurs grupp som skapas när avbildnings mal len skickas till Azure Image Builder-tjänsten. Om du tar bort avbildnings mal len förlorar du den virtuella hård disken. 

## <a name="image-template-operations"></a>Åtgärder för avbildnings mal len

### <a name="starting-an-image-build"></a>Starta en avbildnings version
För att starta en version måste du anropa "kör" på avbildnings mal len resurs, exempel på `run` kommandon:

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>Avbryta en avbildnings version
Om du kör en avbildning som du tror är felaktig, väntar på indata från användaren eller om du tycker att det inte kommer att slutföras korrekt, kan du avbryta versionen.

Versionen kan avbrytas när som helst. Om distributions fasen har startats kan du fortfarande avbryta, men du måste rensa alla avbildningar som inte är klara. Kommandot Cancel väntar inte på att Avbryt ska slutföras, övervaka `lastrunstatus.runstate` för att avbryta förloppet med dessa status [kommandon](image-builder-troubleshoot.md#customization-log).


Exempel på `cancel` kommandon:

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>Nästa steg

Det finns exempel på JSON-filer för olika scenarier i [Azure Image Builder-GitHub](https://github.com/azure/azvmimagebuilder).