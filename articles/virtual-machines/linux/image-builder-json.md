---
title: Skapa en Azure Image Builder mall (förhandsversion)
description: Lär dig hur du skapar en mall som ska användas med Azure Image Builder.
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: 77460d1675b806e04c72e5f46da0ec4274d99d41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762541"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Förhandsversion: Skapa en Azure Image Builder mall 

Azure Image Builder använder en .json-fil för att skicka information till Image Builder tjänsten. I den här artikeln går vi igenom avsnitten i json-filen så att du kan skapa en egen. Exempel på fullständiga .json-filer finns i [Azure Image Builder GitHub](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts).

Det här är det grundläggande mallformatet:

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



## <a name="type-and-api-version"></a>Typ och API-version

är `type` resurstypen, som måste vara `"Microsoft.VirtualMachineImages/imageTemplates"` . ändras `apiVersion` med tiden när API:et ändras, men ska vara `"2020-02-14"` förhandsgranskning.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Location

Platsen är den region där den anpassade avbildningen ska skapas. För Image Builder förhandsversionen stöds följande regioner:

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
Som Image Builder använder en "Standard_D1_v2"-version av en virtuell dator kan du åsidosätta detta. Om du till exempel vill anpassa en avbildning för en virtuell GPU-dator behöver du en GPU VM-storlek. Detta är valfritt.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Som standard Image Builder ändrar inte storleken på bilden. Storleken från källavbildningen används. Du kan **bara** öka storleken på OS-disken (Win och Linux), detta är valfritt och värdet 0 innebär att lämna samma storlek som källavbildningen. Du kan inte minska OS-diskens storlek till mindre än storleken från källavbildningen.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Om du inte anger några VNET-egenskaper skapar Image Builder ett eget VNET, en offentlig IP-adress och en NSG. Den offentliga IP-adressen används för att tjänsten ska kommunicera med den virtuella byggdatorn, men om du inte vill att en offentlig IP-adress eller vill att Image Builder ska ha åtkomst till dina befintliga VNET-resurser, till exempel konfigurationsservrar (DSC, Chef, Puppet, Ansible), filresurser osv., kan du ange ett virtuellt nätverk. Mer information finns i [nätverksdokumentationen](image-builder-networking.md). Detta är valfritt.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Taggar

Det här är nyckel/värde-par som du kan ange för avbildningen som genereras.

## <a name="depends-on-optional"></a>Beror på (valfritt)

Det här valfria avsnittet kan användas för att säkerställa att beroenden slutförs innan du fortsätter. 

```json
    "dependsOn": [],
```

Mer information finns i [Definiera resursberoenden.](../../azure-resource-manager/templates/define-resource-dependency.md#dependson)

## <a name="identity"></a>Identitet

Krävs – för Image Builder ska ha behörighet att läsa/skriva bilder måste du läsa i skript från Azure Storage du måste skapa en Azure User-Assigned Identity som har behörighet till de enskilda resurserna. Mer information om hur Image Builder behörigheter fungerar och relevanta steg finns i [dokumentationen](image-builder-user-assigned-identity.md).


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Image Builder stöd för en User-Assigned Identity:
* Stöder endast en enda identitet
* Stöder inte anpassade domännamn

Mer information finns i Vad [är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md).
Mer information om hur du distribuerar den här funktionen finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Hjälp av Azure CLI.](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)

## <a name="properties-source"></a>Egenskaper: källa

Avsnittet `source` innehåller information om källavbildningen som ska användas av Image Builder. Image Builder för närvarande endast inbyggt stöd för att skapa Avbildningar av Hyper-V-generering (Gen1) 1 till Azure Shared Image Gallery (SIG) eller hanterad avbildning. Om du vill skapa Gen2-avbildningar måste du använda en Gen2-källavbildning och distribuera den till vhd. Därefter måste du skapa en hanterad avbildning från den virtuella hårddisken och mata in den i SIG som en Gen2-avbildning.

API:et kräver en "SourceType" som definierar källan för avbildningsbygget, för närvarande finns det tre typer:
- PlatformImage – angav att källavbildningen är en Marketplace-avbildning.
- ManagedImage – använd det här när du startar från en vanlig hanterad avbildning.
- SharedImageVersion – används när du använder en avbildningsversion i en Shared Image Gallery som källa.


> [!NOTE]
> När du använder befintliga anpassade Windows-avbildningar kan du köra Sysprep-kommandot upp till 8 gånger på en enda Windows-avbildning. Mer information finns i [sysprep-dokumentationen.](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)

### <a name="platformimage-source"></a>PlatformImage-källa 
Azure Image Builder har stöd för Windows Server och -klient, och Linux Azure Marketplace-avbildningar [finns](../image-builder-overview.md#os-support) här för en fullständig lista. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Egenskaperna här är desamma som används för att skapa virtuella datorer med hjälp av AZ CLI och kör nedanstående för att hämta egenskaperna: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Du kan använda "senaste" i versionen. Versionen utvärderas när avbildningsversionen äger rum, inte när mallen skickas. Om du använder den här funktionen med Shared Image Gallery-målet kan du undvika att skicka mallen igen och köra avbildningsbygget igen med jämna mellanrum, så att dina avbildningar återskapas från de senaste avbildningarna.

#### <a name="support-for-market-place-plan-information"></a>Stöd för market place-planinformation
Du kan också ange planinformation, till exempel:
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

Anger källavbildningen som en befintlig hanterad avbildning av en generaliserad virtuell hårddisk eller virtuell dator.

> [!NOTE]
> Den hanterade källavbildningen måste ha ett operativsystem som stöds och avbildningen måste ha samma region som din Azure Image Builder mall. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

ska `imageId` vara ResourceId för den hanterade avbildningen. Använd `az image list` för att visa tillgängliga avbildningar.


### <a name="sharedimageversion-source"></a>SharedImageVersion-källa
Anger källavbildningen till en befintlig avbildningsversion i en Shared Image Gallery.

> [!NOTE]
> Den hanterade källavbildningen måste ha ett operativsystem som stöds och avbildningen måste ha samma region som din Azure Image Builder-mall. Om inte, replikerar du avbildningsversionen till den Image Builder mallregionen.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

ska `imageVersionId` vara ResourceId för avbildningsversionen. Använd [az sig image-version list för](/cli/azure/sig/image-version#az_sig_image_version_list) att visa en lista över avbildningsversioner.


## <a name="properties-buildtimeoutinminutes"></a>Egenskaper: buildTimeoutInMinutes

Som standard körs Image Builder 240 minuter. Därefter går tidsgränsen ut och stoppas, oavsett om avbildningsbygget är klart eller inte. Om tidsgränsen är över visas ett fel som liknar detta:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Om du inte anger något buildTimeoutInMinutes-värde eller anger värdet till 0 används standardvärdet. Du kan öka eller minska värdet, upp till högst 960 minuter (16 timmar). För Windows rekommenderar vi inte att du anger detta under 60 minuter. Om du upptäcker att du har nått tidsgränsen granskar du loggarna [för](image-builder-troubleshoot.md#customization-log)att se om anpassningssteget väntar på något som användarindata. 

Om du upptäcker att du behöver mer tid för att slutföra anpassningar kan du ange det här till det du tror att du behöver, med lite omkostnader. Men ställ inte in den för högt eftersom du kan behöva vänta tills tidsgränsen har gått ut innan du ser ett fel. 


## <a name="properties-customize"></a>Egenskaper: anpassa

Image Builder har stöd för flera "anpassare". Anpassare är funktioner som används för att anpassa avbildningen, till exempel köra skript eller starta om servrar. 

När du använder `customize` : 
- Du kan använda flera anpassare, men de måste ha en unik `name` .
- Anpassare körs i den ordning som anges i mallen.
- Om en anpassare misslyckas kommer hela anpassningskomponenten att misslyckas och rapportera tillbaka ett fel.
- Vi rekommenderar starkt att du testar skriptet noggrant innan du använder det i en mall. Det blir enklare att felsöka skriptet på din egen virtuella dator.
- Placera inte känsliga data i skripten. 
- Skriptplatserna måste vara offentligt tillgängliga, såvida du inte använder [MSI](./image-builder-user-assigned-identity.md).

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

 
Avsnittet anpassa är en matris. Azure Image Builder körs genom anpassarna i sekventiell ordning. Eventuella fel i en anpassare misslyckas med byggprocessen. 

> [!NOTE]
> Infogade kommandon kan visas i bildmallens definition och genom att Microsoft Support när du hjälper till med ett supportfall. Om du har känslig information bör den flyttas till skript i Azure Storage, där åtkomst kräver autentisering.
 
### <a name="shell-customizer"></a>Shell-anpassare

Gränssnittsanpassaren stöder körning av shell-skript. Dessa måste vara offentligt tillgängliga för IB för att komma åt dem.

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

Os-stöd: Linux 
 
Anpassa egenskaper:

- **type** – Shell 
- **name** – namn för spårning av anpassningen 
- **scriptUri** – URI till platsen för filen 
- **infogade –** matris med shell-kommandon, avgränsade med kommatecken.
- **sha256Checksum** – Värdet för sha256-kontrollsumman för filen, du genererar detta lokalt och sedan Image Builder kontrollsumma och validera.
    * Så här genererar du sha256Checksum med hjälp av en terminal på Mac/Linux-körning: `sha256sum <fileName>`

> [!NOTE]
> Infogade kommandon lagras som en del av bildmalldefinitionen. Du kan se dem när du dumpar avbildningsdefinitionen och dessa är också synliga för Microsoft Support i händelse av ett supportfall i felsökningssyfte. Om du har känsliga kommandon eller värden rekommenderar vi starkt att dessa flyttas till skript och använder en användaridentitet för att autentisera till Azure Storage.

#### <a name="super-user-privileges"></a>Superanvändarbehörigheter
För kommandon som ska köras med superanvändarbehörighet måste de föregås av , du kan lägga till dem i skript eller använda det `sudo` infogade kommandon, till exempel:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
Exempel på ett skript med sudo som du kan referera till med scriptUri:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Windows-omstartsanpassare 
Med anpassaren Starta om kan du starta om en virtuell Windows-dator och vänta tills den är online igen. På så sätt kan du installera programvara som kräver en omstart.  

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

Os-stöd: Windows
 
Anpassa egenskaper:
- **Typ:** WindowsRestart
- **restartCommand** – Kommando för att köra omstarten (valfritt). Standardvärdet är `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – Kommando för att kontrollera om omstarten lyckades (valfritt). 
- **restartTimeout** – Tidsgräns för omstart anges som en sträng med storleken och enheten. Till exempel `5m` (5 minuter) eller `2h` (2 timmar). Standardvärdet är: 5 m

### <a name="linux-restart"></a>Linux-omstart  
Det finns ingen anpassare för Linux-omstart, men om du installerar drivrutiner eller komponenter som kräver en omstart kan du installera dem och anropa en omstart med shell-anpassaren. Det finns en SSH-timeout på 20 minuter för den virtuella byggdatorn.

### <a name="powershell-customizer"></a>PowerShell-anpassare 
Shell-anpassaren stöder körning av PowerShell-skript och infogade kommandon. Skripten måste vara offentligt tillgängliga för IB för att komma åt dem.

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

Os-stöd: Windows och Linux

Anpassa egenskaper:

- **type** – PowerShell.
- **scriptUri** – URI till platsen för PowerShell-skriptfilen. 
- **inline** – Infogade kommandon som ska köras, avgränsade med kommatecken.
- **validExitCodes** – Valfria, giltiga koder som kan returneras från skriptet/det infogade kommandot, detta undviker rapporterade fel i skriptet/det infogade kommandot.
- **runElevated** – Valfritt, booleskt stöd för att köra kommandon och skript med förhöjd behörighet.
- **sha256Checksum** – Värdet för sha256-kontrollsumman för filen, du genererar detta lokalt och sedan Image Builder kontrollsumma och validera.
    * Generera sha256Checksum med hjälp av en PowerShell i Windows [Get-Hash](/powershell/module/microsoft.powershell.utility/get-filehash)


### <a name="file-customizer"></a>Filanpassare

Med filanpassaren kan bildverktyget ladda ned en fil från en GitHub- eller Azure-lagring. Om du har en pipeline för avbildningsbygge som förlitar sig på byggartefakter kan du ange att filanpassaren ska ladda ned från byggresursen och flytta artefakterna till avbildningen.  

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

Os-stöd: Linux och Windows 

Egenskaper för filanpassare:

- **sourceUri** – en tillgänglig lagringsslutpunkt, detta kan vara GitHub eller Azure Storage. Du kan bara ladda ned en fil, inte en hel katalog. Om du behöver ladda ned en katalog använder du en komprimerad fil och avmarkerar den sedan med shell- eller PowerShell-anpassare. 

> [!NOTE]
> Om sourceUri är ett Azure Storage-konto, oavsett om bloben är markerad som offentlig, kommer du att ge den hanterade användaridentiteten behörighet att läsa åtkomst på bloben. Se det här [exemplet för](./image-builder-user-assigned-identity.md#create-a-resource-group) att ange lagringsbehörigheter.

- **destination** – det här är den fullständiga målsökvägen och filnamnet. Alla refererade sökvägar och underkataloger måste finnas. Använd Shell- eller PowerShell-anpassare för att konfigurera dessa i förväg. Du kan använda skriptanpassare för att skapa sökvägen. 

Detta stöds av Windows-kataloger och Linux-sökvägar, men det finns några skillnader: 
- Linux-operativsystem – den enda sökväg som Image Builder kan skriva till är /tmp.
- Windows – Det finns ingen begränsning för sökvägen, men sökvägen måste finnas.
 
 
Om det uppstår ett fel när du försöker ladda ned filen eller lägger den i en angiven katalog misslyckas steget anpassa, och detta kommer att finnas i customization.log.

> [!NOTE]
> Filanpassaren är endast lämplig för små filnedladdningar, < 20 MB. För större filnedladdningar använder du ett skript eller ett infogade kommando. Använd koden för att ladda ned filer, till exempel Linux `wget` eller `curl` , Windows, `Invoke-WebRequest` .

### <a name="windows-update-customizer"></a>Windows Update Anpassare
Den här anpassaren bygger på [communityn Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) för Packer, vilket är ett projekt med öppen källkod som underhålls av Packer-communityn. Microsoft testar och validerar etableringen med Image Builder-tjänsten och stöder undersökning av problem med den och arbetar för att lösa problem, men projektet med öppen källkod stöds inte officiellt av Microsoft. Detaljerad dokumentation om och hjälp med Windows Update Provisioner finns i projektdatabasen.

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
- **type**  – WindowsUpdate.
- **searchCriteria** – Valfri, definierar vilken typ av uppdateringar som installeras (rekommenderas, viktigt osv.), BrowseOnly =0 och IsInstalled=0 (rekommenderas) är standardvärdet.
- **filters** – Valfritt, låter dig ange ett filter som ska inkludera eller exkludera uppdateringar.
- **updateLimit** – Valfritt definierar hur många uppdateringar som kan installeras, standardvärdet 1000.
 
> [!NOTE]
> Den Windows Update kan misslyckas om det finns utestående Windows-omstarter eller programinstallationer som fortfarande körs, vanligtvis kan du se det här felet i customization.log, `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` . Vi rekommenderar starkt att du överväger att lägga till i en omstart [](/powershell/module/microsoft.powershell.utility/start-sleep) av Windows och/eller tillåta program tillräckligt med tid för att slutföra sina installationer med hjälp av kommandon för strömsparläge eller vänteläge i infogade kommandon eller skript innan de Windows Update.

### <a name="generalize"></a>Generalisera 
Som standard kör Azure Image Builder även avetablera kod i slutet av varje avbildningsanpassningsfas för att "generalisera" avbildningen. Generalisering är en process där avbildningen har ställts in så att den kan återanvändas för att skapa flera virtuella datorer. För virtuella Windows-datorer använder Azure Image Builder Sysprep. För Linux kör Azure Image Builder "waagent -deprovision". 

Kommandona Image Builder som ska generaliseras kanske inte är lämpliga för alla situationer, så i Azure Image Builder kan du anpassa det här kommandot om det behövs. 

Om du migrerar befintlig anpassning och du använder olika Sysprep-/waagent-kommandon kan du använda de allmänna Image Builder-kommandona. Om det inte går att skapa den virtuella datorn använder du dina egna Sysprep- eller waagent-kommandon.

Om Azure Image Builder skapar en anpassad Windows-avbildning och du skapar en virtuell dator från den och sedan upptäcker att skapandet av den virtuella datorn misslyckas eller inte slutförs korrekt, måste du granska Windows Server Sysprep-dokumentationen eller skapa en supportbegäran hos supportteamet för Windows Server Sysprep Customer Services, som kan felsöka och ge råd om rätt Sysprep-användning.


#### <a name="default-sysprep-command"></a>Sysprep-standardkommando
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
#### <a name="default-linux-deprovision-command"></a>Standardkommando för Linux-avetablera

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Åsidosätta kommandona
Om du vill åsidosätta kommandona använder du PowerShell- eller Shell-skriptetablerna för att skapa kommandofilerna med det exakta filnamnet och placera dem i rätt kataloger:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder dessa kommandon skrivs ut till AIB-loggarna, "customization.log". Se [felsökning om](image-builder-troubleshoot.md#customization-log) hur du samlar in loggar.
 
## <a name="properties-distribute"></a>Egenskaper: distribuera

Azure Image Builder stöder tre distributionsmål: 

- **managedImage –** hanterad avbildning.
- **sharedImage** – Shared Image Gallery.
- **VHD** – VHD i ett lagringskonto.

Du kan distribuera en avbildning till båda måltyperna i samma konfiguration.

> [!NOTE]
> Standardkommandot för AIB sysprep inkluderar inte "/mode:vm", men detta kan krävas när du skapar avbildningar som har HyperV-rollen installerad. Om du behöver lägga till det här kommandoargumentet måste du åsidosätta sysprep-kommandot.

Eftersom du kan ha fler än ett mål att distribuera till Image Builder du ett tillstånd för varje distributionsmål som kan nås genom att fråga `runOutputName` .  är `runOutputName` ett -objekt som du kan fråga efter distributionen om du vill ha information om den distributionen. Du kan till exempel fråga platsen för den virtuella hårddisken eller regioner där avbildningsversionen replikerades eller SIG-avbildningsversionen har skapats. Det här är en egenskap för varje distributionsmål. måste `runOutputName` vara unikt för varje distributionsmål. Här är ett exempel på en fråga Shared Image Gallery distribution:

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

Avbildningsutdata är en resurs för hanterade avbildningar.

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
- **type** – managedImage 
- **imageId** – Resurs-ID för målavbildningen, förväntat format: /subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **location** – platsen för den hanterade avbildningen.  
- **runOutputName** – unikt namn för att identifiera distributionen.  
- **artifactTags –** Valfria användarangivna nyckelvärdepartaggar.
 
 
> [!NOTE]
> Målresursgruppen måste finnas.
> Om du vill att avbildningen ska distribueras till en annan region, ökar distributionstiden . 

### <a name="distribute-sharedimage"></a>Distribuera: sharedImage 
Azure Shared Image Gallery är en ny avbildningshanteringstjänst som gör det möjligt att hantera replikering av avbildningsregion, versionshantering och delning av anpassade avbildningar. Azure Image Builder har stöd för distribution med den här tjänsten, så att du kan distribuera avbildningar till regioner som stöds av Delade bildgallerier. 
 
En Shared Image Gallery består av: 
 
- Galleri – Container för flera delade avbildningar. Ett galleri distribueras i en region.
- Bilddefinitioner – en konceptuell gruppering för bilder. 
- Avbildningsversioner – det här är en avbildningstyp som används för att distribuera en virtuell dator eller skalningsuppsättning. Avbildningsversioner kan replikeras till andra regioner där virtuella datorer måste distribueras.
 
Innan du kan distribuera till bildgalleriet måste du skapa ett galleri och en bilddefinition. Se [Delade bilder](../shared-images-cli.md). 

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

Distribuera egenskaper för delade bildgallerier:

- **type** – sharedImage  
- **galleryImageId** – ID för det delade bildgalleriet, detta kan anges i två format:
    * Automatisk versionshantering – Image Builder genererar ett monotont versionsnummer åt dig. Detta är användbart när du vill fortsätta återskapa bilder från samma mall: Formatet är: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Explicit versionshantering – Du kan skicka in det versionsnummer som du vill att image builder ska använda. Formatet är: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** – unikt namn för att identifiera distributionen.  
- **artifactTags –** Valfria användarangivna nyckelvärdepartaggar.
- **replicationRegions** – matris med regioner för replikering. En av regionerna måste vara den region där galleriet distribueras. Att lägga till regioner innebär en ökning av byggtiden eftersom bygget inte slutförs förrän replikeringen har slutförts.
- **excludeFromLatest** (valfritt) Med det här alternativet kan du markera den avbildningsversion som du skapar och inte användas som den senaste versionen i SIG-definitionen. Standardvärdet är "false".
- **storageAccountType** (valfritt) AIB har stöd för att ange dessa typer av lagring för den avbildningsversion som ska skapas:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Om avbildningsmallen `image definition` och den refererade inte finns på samma plats visas ytterligare tid för att skapa avbildningar. Image Builder för närvarande inte har någon `location` parameter för resursen för avbildningsversion tar vi den från dess överordnade `image definition` . Om en avbildningsdefinition till exempel finns i usa, västra och du vill att avbildningsversionen ska replikeras till eastus, kopieras en blob till usa, västra usa, från detta skapas en avbildningsversionsresurs i USA, västra och replikeras sedan till eastus. För att undvika den extra replikeringstiden ser du till `image definition` att avbildningsmallen och finns på samma plats.


### <a name="distribute-vhd"></a>Distribuera: VHD  
Du kan mata ut till en virtuell hårddisk. Du kan sedan kopiera den virtuella hårddisken och använda den för att publicera till Azure MarketPlace eller använda den med Azure Stack.  

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
 
Os-stöd: Windows och Linux

Distribuera VHD-parametrar:

- **type** – VHD.
- **runOutputName** – unikt namn för att identifiera distributionen.  
- **tags** – Valfria taggar för nyckel/värde-par som anges av användaren.
 
Azure Image Builder tillåter inte att användaren anger en lagringskontoplats, men du kan fråga efter status för `runOutputs` för att hämta platsen.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> När den virtuella hårddisken har skapats kopierar du den till en annan plats så snart som möjligt. Den virtuella hårddisken lagras i ett lagringskonto i den tillfälliga resursgrupp som skapas när avbildningsmallen skickas till Azure Image Builder-tjänsten. Om du tar bort avbildningsmallen förlorar du den virtuella hårddisken. 

## <a name="image-template-operations"></a>Åtgärder för bildmall

### <a name="starting-an-image-build"></a>Starta en avbildningsbygge
Om du vill starta en version måste du anropa "Kör" på resursen Avbildningsmall, exempel på `run` kommandon:

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

### <a name="cancelling-an-image-build"></a>Avbryta en avbildningsbygge
Om du kör ett avbildningsbygge som du tror är felaktigt, väntar på användarindata eller om du tror att det aldrig kommer att slutföras, kan du avbryta bygget.

Bygget kan avbrytas när som helst. Om distributionsfasen har startat kan du fortfarande avbryta, men du måste rensa alla avbildningar som kanske inte har slutförts. Avbryt-kommandot väntar inte tills avbryt har slutförts. Du kan övervaka om förloppet `lastrunstatus.runstate` avbryts med hjälp av dessa [statuskommandon](image-builder-troubleshoot.md#customization-log).


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

Det finns .json-exempelfiler för olika scenarier i [Azure Image Builder GitHub](https://github.com/azure/azvmimagebuilder).