---
title: Azure Compute – Linux Diagnostic-tillägg 3,0
description: Så här konfigurerar du LAD (Azure Linux Diagnostic Extension) 3,0 för att samla in mått och logg händelser från virtuella Linux-datorer som körs i Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.openlocfilehash: d063aec3b093f00640d909a6ce3c2cde6d2d2420
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547432"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Använd Linux Diagnostic Extension 3,0 för att övervaka mått och loggar

I det här dokumentet beskrivs version 3,0 och senare av Linux-diagnostik.

> [!IMPORTANT]
> Information om version 2,3 och äldre finns i [det här dokumentet](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Introduktion

Tillägget för Linux-diagnostik hjälper en användare att övervaka hälsan hos en virtuell Linux-dator som körs på Microsoft Azure. Den har följande funktioner:

* Samlar in system prestanda mått från den virtuella datorn och lagrar dem i en viss tabell i ett särskilt lagrings konto.
* Hämtar logg händelser från syslog och lagrar dem i en viss tabell i det angivna lagrings kontot.
* Gör det möjligt för användare att anpassa de data mått som samlas in och överförs.
* Gör det möjligt för användare att anpassa syslog-funktionerna och allvarlighets graden för händelser som samlas in och överförs.
* Gör det möjligt för användare att överföra angivna loggfiler till en angiven lagrings tabell.
* Stöder sändning av mått och logg händelser till godtyckliga EventHub-slutpunkter och JSON-formaterade blobbar i det angivna lagrings kontot.

Det här tillägget fungerar med både Azures distributions modeller.

## <a name="installing-the-extension-in-your-vm"></a>Installera tillägget på din virtuella dator

Du kan aktivera det här tillägget med hjälp av Azure PowerShell-cmdletar, Azure CLI-skript, ARM-mallar eller Azure Portal. Mer information finns i [tillägg-funktioner](features-linux.md).

>[!NOTE]
>Vissa komponenter i det virtuella diagnostik-tillägget för virtuella datorer levereras också i [Log Analytics VM-tillägget](./oms-linux.md). På grund av den här arkitekturen kan konflikter uppstå om båda tilläggen instansieras i samma ARM-mall. Undvik dessa installations tids konflikter genom att använda [ `dependsOn` direktivet](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) för att se till att tilläggen installeras i tur och ordning. Tilläggen kan installeras i valfri ordning.

Dessa installationsinstruktioner och en [nedladdnings bar exempel konfiguration](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) konfigurerar lad 3,0 till:

* avbilda och lagra samma mått som tillhandahölls av LAD 2,3;
* avbilda en användbar uppsättning med fil Systems mått, nytt till LAD 3,0;
* avbilda Standard-syslog-samlingen som aktive ras av LAD 2,3;
* Aktivera Azure Portal upplevelse för diagram och aviseringar på VM-mått.

Den nedladdnings bara konfigurationen är bara ett exempel. ändra den så att den passar dina egna behov.

### <a name="supported-linux-distributions"></a>Linux-distributioner som stöds

Tillägget för Linux-diagnostik stöder följande distributioner och versioner. Listan över distributioner och versioner gäller endast för Azure-godkända Linux-leverantörs avbildningar. BYOL-och BYOS-avbildningar från tredje part, t. ex. enheter, stöds vanligt vis inte för Linux Diagnostic-tillägget.

En distribution som endast listar huvud versioner, som Debian 7, stöds också för alla del versioner. Om en specifik del version anges stöds endast den specifika versionen. om "+" läggs till stöds lägre versioner som är lika med eller större än den angivna versionen.

Distributioner och versioner som stöds:

- Ubuntu 18,04, 16,04, 14,04
- CentOS 7, 6.5 +
- Oracle Linux 7, 6.4 +
- OpenSUSE 13.1 +
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- RHEL 7, 6,7 +

### <a name="prerequisites"></a>Förutsättningar

* **Azure Linux-agentens version 2.2.0 eller senare**. De flesta Azure VM Linux-avbildningar innehåller version 2.2.7 eller senare. Kör `/usr/sbin/waagent -version` för att bekräfta versionen som är installerad på den virtuella datorn. Om den virtuella datorn kör en äldre version av gäst agenten följer du [de här anvisningarna](./update-linux-agent.md) för att uppdatera den.
* **Azure CLI**. [Konfigurera Azure CLI](/cli/azure/install-azure-cli) -miljön på din dator.
* Kommandot wget, om du inte redan har det: kör `sudo apt-get install wget` .
* En befintlig Azure-prenumeration och ett befintligt allmänt lagrings konto för lagring av data i.  Lagrings konton för generell användning stöder tabell lagring som krävs.  Ett Blob Storage-konto kommer inte att fungera.
* Python 2

### <a name="python-requirement"></a>Python-krav

Tillägget för Linux-diagnostik kräver python 2. Om den virtuella datorn använder en distribution som inte innehåller python 2 som standard måste du installera den. Följande exempel kommandon kommer att installera python 2 på olika distributioner.    

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE `zypper install -y python2`

Den körbara filen python2 måste ha ett alias till *python*. Följande är en metod som du kan använda för att ange det här aliaset:

1. Kör följande kommando för att ta bort alla befintliga alias.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Kör följande kommando för att skapa aliaset.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Exempel installation

> [!NOTE]
> För något av exemplen fyller du i rätt värden för variablerna i det första avsnittet innan du kör. 

Exempel konfigurationen som hämtas i de här exemplen samlar in en uppsättning standard data och skickar dem till Table Storage. URL: en för exempel konfigurationen och dess innehåll kan komma att ändras. I de flesta fall bör du ladda ned en kopia av JSON-filen med Portal inställningar och anpassa den efter dina behov. därefter har du alla mallar eller automatiseringar som du skapar med din egen version av konfigurations filen i stället för att hämta URL: en varje tillfälle.

#### <a name="azure-cli-sample"></a>Azure CLI-exempel

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-30-extension-on-the-virtual-machine-scale-set-instance"></a>Azure CLI-exempel för att installera LAD 3,0-tillägget på den virtuella datorns skalnings uppsättnings instans

```azurecli
#Set your Azure VMSS diagnostic variables correctly below
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VMSS resource ID. Replace storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell-exempel

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>Uppdaterar tilläggs inställningarna

När du har ändrat dina skyddade eller offentliga inställningar distribuerar du dem till den virtuella datorn genom att köra samma kommando. Om något ändras i inställningarna skickas de uppdaterade inställningarna till tillägget. LAD laddar om konfigurationen och startar om sig själv.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrering från tidigare versioner av tillägget

Den senaste versionen av tillägget är **3,0**. **Alla gamla versioner (2. x) är inaktuella och kan tas bort från och med den 31 juli 2018**.

> [!IMPORTANT]
> Tillägget introducerar ändringar i tilläggets konfiguration. En sådan ändring gjordes för att förbättra säkerheten för tillägget. Det innebär att det inte går att behålla bakåtkompatibilitet med 2. x. Tilläggs utgivaren för det här tillägget skiljer sig också från utgivaren för 2. x-versionerna.
>
> Om du vill migrera från 2. x till den här nya versionen av tillägget måste du avinstallera det gamla tillägget (under det gamla utgivar namnet) och sedan installera version 3 av tillägget.

Rekommendationer:

* Installera tillägget med automatisk uppgradering av lägre version aktiverat.
  * På klassiska virtuella datorer för distributions modell anger du ' 3. * ' som version om du installerar tillägget via Azure XPLAT CLI eller PowerShell.
  * På Azure Resource Manager distributions modellens virtuella datorer inkluderar du "" aktiverat autoupgrademinorversion ": true" i mallen för VM-distribution.
* Använd ett nytt/annat lagrings konto för LAD 3,0. Det finns flera små inkompatibiliteter mellan LAD 2,3 och LAD 3,0 som gör det enkelt att dela ett konto problematiska:
  * LAD 3,0 lagrar Syslog-händelser i en tabell med ett annat namn.
  * CounterSpecifier-strängarna för `builtin` mått skiljer sig åt i LAD 3,0.

## <a name="protected-settings"></a>Skyddade inställningar

Den här uppsättningen konfigurations information innehåller känslig information som bör skyddas från offentlig vy, till exempel autentiseringsuppgifter för lagring. De här inställningarna överförs till och lagras av tillägget i krypterad form.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | Värde
---- | -----
storageAccountName | Namnet på det lagrings konto där data skrivs av tillägget.
storageAccountEndPoint | valfritt Slut punkten som identifierar molnet där lagrings kontot finns. Om den här inställningen saknas, LAD standardvärdet för det offentliga Azure-molnet `https://core.windows.net` . Om du vill använda ett lagrings konto i Azure Germany, Azure Government eller Azure Kina anger du detta värde i enlighet med detta.
storageAccountSasToken | En [SAS-token](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) för blob och table Services ( `ss='bt'` ) som gäller för behållare och objekt ( `srt='co'` ), som ger behörigheterna Lägg till, skapa, lista, uppdatera och skriv ( `sp='acluw'` ). Ta *inte* med det inledande fråga-tecknet (?).
mdsdHttpProxy | valfritt Information om HTTP-proxy som krävs för att aktivera tillägget för att ansluta till det angivna lagrings kontot och slut punkten.
sinksConfig | valfritt Information om alternativa destinationer till vilka mått och händelser som kan levereras. Detaljerad information om varje data mottagare som stöds av tillägget beskrivs i avsnitten som följer.

Om du vill hämta en SAS-token i en Resource Manager-mall använder du funktionen **listAccountSas** . En exempel-mall finns i [exempel på en lista funktion](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Du kan enkelt skapa den SAS-token som krävs via Azure Portal.

1. Välj det allmänna lagrings konto som du vill att tillägget ska skrivas till
1. Välj "signatur för delad åtkomst" från inställningar-delen av den vänstra menyn
1. Gör lämpliga avsnitt enligt beskrivningen ovan
1. Klicka på knappen generera SAS.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Skärm bild som visar sidan signatur för delad åtkomst med generera S.":::

Kopiera de genererade SAS: erna till fältet storageAccountSasToken; ta bort det inledande fråga-tecknet ("?").

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Det här valfria avsnittet definierar ytterligare destinationer dit tillägget skickar den information som samlas in. Matrisen "Sink" innehåller ett objekt för varje ytterligare data mottagare. Attributet "Type" bestämmer de andra attributen i objektet.

Element | Värde
------- | -----
name | En sträng som används för att referera till denna mottagare någon annan stans i tilläggs konfigurationen.
typ | Typ av mottagare som definieras. Bestämmer de andra värdena (om sådana finns) i instanser av den här typen.

Version 3,0 av Linux Diagnostic-tillägget har stöd för två typer av mottagare: EventHub och JsonBlob.

#### <a name="the-eventhub-sink"></a>EventHub-sinken

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

Posten "sasURL" innehåller den fullständiga URL: en, inklusive SAS-token, för den Händelsehubben som data ska publiceras till. LAD kräver en SAS som namnger en princip som aktiverar Send-anspråk. Ett exempel:

* Skapa ett Event Hubs-namnområde som kallas `contosohub`
* Skapa en Event Hub i namn området som kallas `syslogmsgs`
* Skapa en princip för delad åtkomst på Händelsehubben med namnet `writer` som aktiverar Send-anspråk

Om du har skapat ett SAS-värde till och med midnatt UTC den 1 januari 2018 kan sasURL-värdet vara:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Mer information om att skapa och hämta information om SAS-token för Event Hubs finns på [den här webb sidan](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="the-jsonblob-sink"></a>JsonBlob-mottagare

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data som dirigeras till en JsonBlob-mottagare lagras i blobbar i Azure Storage. Varje instans av LAD skapar en BLOB varje timme för varje mottagar namn. Varje Blob innehåller alltid en syntaktiskt giltig JSON-matris med objekt. Nya poster läggs atomict till i matrisen. Blobbar lagras i en behållare med samma namn som mottagaren. Reglerna för Azure Storage för BLOB container-namn gäller för namnen på JsonBlob-mottagare: mellan 3 och 63 gemena alfanumeriska ASCII-tecken eller bindestreck.

## <a name="public-settings"></a>Offentliga inställningar

Den här strukturen innehåller olika block med inställningar som styr den information som samlas in av tillägget. Varje inställning är valfri. Om du anger `ladCfg` måste du också ange `StorageAccount` .

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Element | Värde
------- | -----
StorageAccount | Namnet på det lagrings konto där data skrivs av tillägget. Måste vara samma namn som anges i de [skyddade inställningarna](#protected-settings).
mdsdHttpProxy | valfritt Samma som i de [skyddade inställningarna](#protected-settings). Det offentliga värdet åsidosätts av det privata värdet, om det är inställt. Placera proxyinställningar som innehåller en hemlighet, till exempel ett lösen ord, i de [skyddade inställningarna](#protected-settings).

Återstående element beskrivs i detalj i följande avsnitt.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Den här valfria strukturen styr insamlingen av mått och loggar för leverans till Azure Metrics-tjänsten och till andra data mottagare. Du måste ange antingen `performanceCounters` eller `syslogEvents` eller båda. Du måste ange `metrics` strukturen.

Element | Värde
------- | -----
eventVolume | valfritt Styr antalet partitioner som skapats i lagrings tabellen. Måste vara en av `"Large"` , `"Medium"` eller `"Small"` . Om inget värde anges är standardvärdet `"Medium"` .
sampleRateInSeconds | valfritt Standard intervallet mellan samling av RAW-mått (unaggregerade). Den minsta samplings frekvensen som stöds är 15 sekunder. Om inget värde anges är standardvärdet `15` .

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Element | Värde
------- | -----
resourceId | Azure Resource Manager resurs-ID för den virtuella datorn eller den virtuella datorns skalnings uppsättning som den virtuella datorn tillhör. Den här inställningen måste också anges om en JsonBlob-mottagare används i konfigurationen.
scheduledTransferPeriod | Den frekvens med vilken aggregerade mått ska beräknas och överföras till Azure-mått, uttryckt som ett 8601-tidsintervall. Den minsta överförings perioden är 60 sekunder, det vill säga PT1M. Du måste ange minst en scheduledTransferPeriod.

Exempel på de mått som anges i avsnittet performanceCounters samlas in var 15: e sekund eller vid den samplings frekvens som uttryckligen definierats för räknaren. Om flera scheduledTransferPeriod frekvenser visas (som i exemplet) beräknas varje agg regering oberoende av varandra.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Det här valfria avsnittet styr insamlingen av mått. RAW-exempel sammanställs för varje [scheduledTransferPeriod](#metrics) för att producera följande värden:

* medelvärde
* min
* max
* senast insamlat värde
* antal RAW-exempel som används för att beräkna mängden

Element | Värde
------- | -----
mottagare | valfritt En kommaavgränsad lista över namn på mottagare som LAD skickar sammanställda mått resultat till. Alla sammansatta mått publiceras till varje mottagen mottagare. Se [sinksConfig](#sinksconfig). Exempel: `"EHsink1, myjsonsink"`.
typ | Identifierar den faktiska providern för måttet.
klass | Tillsammans med "Counter" identifierar det angivna måttet i namn området för providern.
räknare | Tillsammans med "Class" identifierar det angivna måttet i namn området för providern.
counterSpecifier | Identifierar det speciella måttet i namn området för Azure-mått.
moduletype | valfritt Väljer en speciell instans av objektet som måttet gäller för eller väljer agg regeringen för alla instanser av objektet. Mer information finns i `builtin` mått definitionerna.
sampleRate | ÄR 8601 intervall som anger med vilken hastighet rå samplingar för det här måttet samlas in. Om den inte anges anges samlings intervallet av värdet för [sampleRateInSeconds](#ladcfg). Den kortaste samplings frekvensen är 15 sekunder (PT15S).
unit | Måste vara en av följande strängar: "count", "bytes", "Seconds", "percent", "CountPerSecond", "BytesPerSecond", "Millisekunde". Definierar måttets enhet. Användare av insamlade data förväntar sig insamlade datavärden som matchar den här enheten. LAD ignorerar det här fältet.
displayName | Etiketten (på det språk som anges av den associerade språk inställningen) som ska kopplas till dessa data i Azure-mått. LAD ignorerar det här fältet.

CounterSpecifier är en godtycklig identifierare. Konsumenter av mått, som Azure Portal funktion för diagram och avisering, använder counterSpecifier som "Key" som identifierar ett mått eller en instans av ett mått. För `builtin` mått rekommenderar vi att du använder counterSpecifier-värden som börjar med `/builtin/` . Om du samlar in en speciell instans av ett mått rekommenderar vi att du kopplar instansens identifierare till counterSpecifier-värdet. Några exempel:

* `/builtin/Processor/PercentIdleTime` -Inaktiv tid i genomsnitt för alla virtuella processorer
* `/builtin/Disk/FreeSpace(/mnt)` – Ledigt utrymme för/mnt-filsystem
* `/builtin/Disk/FreeSpace` – Genomsnittligt utrymme i genomsnitt i alla monterade fil system

Varken LAD eller Azure Portal förväntar sig att counterSpecifier-värdet matchar eventuella mönster. Var konsekvent i hur du skapar counterSpecifier-värden.

När du anger `performanceCounters` skriver lad alltid data till en tabell i Azure Storage. Du kan ha samma data som skrivits till JSON-blobbar och/eller Event Hubs, men du kan inte inaktivera lagring av data i en tabell. Alla instanser av diagnostiskt tillägg som kon figurer ATS för att använda samma lagrings konto namn och slut punkt lägger till sina mått och loggar i samma tabell. Om för många virtuella datorer skrivs till samma Table-partition kan Azure begränsa skrivningar till den partitionen. Inställningen eventVolume gör att poster sprids över 1 (små), 10 (medel) eller 100 (stora) olika partitioner. Normalt räcker "medium" för att säkerställa att trafiken inte begränsas. Azure Metrics-funktionen i Azure Portal använder data i den här tabellen för att skapa grafer eller utlösa aviseringar. Tabell namnet är sammanfogningen av dessa strängar:

* `WADMetrics`
* "ScheduledTransferPeriod" för de sammanställda värdena som lagras i tabellen
* `P10DV2S`
* Ett datum, i formatet "ÅÅÅÅMMDD", som ändras var 10: e dag

Exempel: `WADMetricsPT1HP10DV2S20170410` och `WADMetricsPT1MP10DV2S20170609` .

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Det här valfria avsnittet styr samlingen av logg händelser från syslog. Om avsnittet utelämnas, samlas inga Syslog-händelser in alls.

SyslogEventConfiguration-samlingen har en post för varje syslog-funktion. Om minSeverity är "ingen" för en viss anläggning, eller om denna funktion inte visas i elementet alls, så fångas inga händelser från den funktionen.

Element | Värde
------- | -----
mottagare | En kommaavgränsad lista över namn på mottagare som enskilda logg händelser publiceras till. Alla logg händelser som matchar begränsningarna i syslogEventConfiguration publiceras till varje mottagen mottagare. Exempel: "EHforsyslog"
facilityName | Ett syslog-servernamn (till exempel "LOG \_ User" eller "log \_ LOCAL0"). Se avsnittet "anläggning" på [sidan syslog-man](http://man7.org/linux/man-pages/man3/syslog.3.html) för den fullständiga listan.
minSeverity | En syslog-allvarlighets nivå (till exempel "LOG \_ Err" eller "logg \_ information"). Se avsnittet "nivå" på [sidan syslog-man](http://man7.org/linux/man-pages/man3/syslog.3.html) för den fullständiga listan. Tillägget fångar händelser som skickas till anläggningen på eller över den angivna nivån.

När du anger `syslogEvents` skriver lad alltid data till en tabell i Azure Storage. Du kan ha samma data som skrivits till JSON-blobbar och/eller Event Hubs, men du kan inte inaktivera lagring av data i en tabell. Partitionerings beteendet för den här tabellen är detsamma som beskrivs för `performanceCounters` . Tabell namnet är sammanfogningen av dessa strängar:

* `LinuxSyslog`
* Ett datum, i formatet "ÅÅÅÅMMDD", som ändras var 10: e dag

Exempel: `LinuxSyslog20170410` och `LinuxSyslog20170609` .

### <a name="perfcfg"></a>perfCfg

Det här valfria avsnittet styr körningen av godtyckliga [OMI](https://github.com/Microsoft/omi) -frågor.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Element | Värde
------- | -----
namnområde | valfritt OMI-namnområdet som frågan ska köras inom. Om inget anges är standardvärdet "root/SCX", implementerat av [System Center cross-platform-leverantörer](https://github.com/Microsoft/SCXcore).
DocumentDB | OMI-frågan som ska köras.
tabell | valfritt Azure Storage-tabellen i det angivna lagrings kontot (se [skyddade inställningar](#protected-settings)).
frequency | valfritt Antalet sekunder mellan körningen av frågan. Standardvärdet är 300 (5 minuter); Minimivärdet är 15 sekunder.
mottagare | valfritt En kommaavgränsad lista över namn på ytterligare mottagare som rå samplings mått resultat ska publiceras i. Ingen agg regering av dessa RAW-exempel beräknas av tillägget eller av Azure-mått.

Du måste ange antingen "table" eller "Sinks" eller båda.

### <a name="filelogs"></a>fileLogs

Styr avbildningen av loggfiler. LAD fångar nya text rader när de skrivs till filen och skriver dem till tabell rader och/eller angivna handfat (JsonBlob eller EventHub).

> [!NOTE]
> fileLogs fångas upp av en del komponent i LAD som anropas `omsagent` . För att kunna samla in fileLogs måste du se till att `omsagent` användaren har Läs behörighet för de filer som du anger, samt kör behörigheter för alla kataloger i sökvägen till filen. Du kan kontrol lera detta genom att köra `sudo su omsagent -c 'cat /path/to/file'` när lad har installerats.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Element | Värde
------- | -----
file | Den fullständiga sökvägen till logg filen som ska bevakas och fångas. Sökvägen måste ha ett namn på en enskild fil. den kan inte namnge en katalog eller innehålla jokertecken. Användar kontot "omsagent" måste ha Läs behörighet till fil Sök vägen.
tabell | valfritt Azure Storage-tabellen, i det angivna lagrings kontot (enligt vad som anges i den skyddade konfigurationen), som nya rader från "änden" av filen skrivs till.
mottagare | valfritt En kommaavgränsad lista över namn på ytterligare mottagare som logg rader skickas till.

Du måste ange antingen "table" eller "Sinks" eller båda.

## <a name="metrics-supported-by-the-builtin-provider"></a>Mått som stöds av den inbyggda providern

Den inbyggda mått leverantören är en källa till mått som är mest intressanta för en bred uppsättning användare. Dessa mått delas in i fem breda klasser:

* Processor
* Minne
* Nätverk
* Filsystem
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>inbyggda mått för processor klassen

Processor klassen för mått ger information om processor användning på den virtuella datorn. När procent andelen beräknas är resultatet genomsnittet för alla CPU: er. I en vCPU virtuell dator, om en vCPU var 100% upptagen och den andra var 100% inaktiv, skulle den rapporterade PercentIdleTime vara 50. Om varje vCPU var 50% upptagen för samma period skulle det rapporterade resultatet också vara 50. I en vCPU virtuell dator med fyra virtuella datorer, med en vCPU på 100% upptagen och andra inaktiva, skulle den rapporterade PercentIdleTime vara 75.

räknare | Innebörd
------- | -------
PercentIdleTime | Procent andel av tiden under agg regerings perioden som processorerna körde kernel Idle-slingan
PercentProcessorTime | Procent andel av tiden som en icke-inaktiv tråd körs
PercentIOWaitTime | Procent andel av tid i väntan på att IO-åtgärder ska slutföras
PercentInterruptTime | Procent andel av tiden som maskin vara/program avbrott och DPC-anrop (uppskjutna steg) körs
PercentUserTime | Vid icke-inaktivitet under agg regerings perioden, den procent andel av tiden som ägnats åt användare mer med normal prioritet
PercentNiceTime | För icke-inaktivitet, procent andelen för sänkt (snyggt) prioritet
PercentPrivilegedTime | För icke-inaktivitet, procent andelen förbrukat i kernelläge

De första fyra räknarna ska summera till 100%. De sista tre räknarna summerar också till 100%; de sammanslager summan av PercentProcessorTime, PercentIOWaitTime och PercentInterruptTime.

Om du vill hämta en enda mått mängd i alla processorer anger du `"condition": "IsAggregate=TRUE"` . Om du vill få ett mått för en speciell processor, till exempel den andra logiska processorn för en vCPU virtuell dator, anger du `"condition": "Name=\\"1\\""` . Logiska processor nummer är i intervallet `[0..n-1]` .

### <a name="builtin-metrics-for-the-memory-class"></a>inbyggda mått för minnes klassen

Minnes klassen för mått ger information om minnes användning, växling och växling.

räknare | Innebörd
------- | -------
AvailableMemory | Tillgängligt fysiskt minne i MiB
PercentAvailableMemory | Tillgängligt fysiskt minne som en procent andel av det totala minnet
UsedMemory | Förbrukat fysiskt minne (MiB)
PercentUsedMemory | Fysiskt minne i bruk som en procent andel av det totala minnet
PagesPerSec | Total växling (Läs/skriv)
PagesReadPerSec | Sidor läses från lagrings plats (växlings fil, program fil, mappad fil osv.)
PagesWrittenPerSec | Sidor som skrivs till lagrings platsen (växlings fil, mappad fil osv.)
AvailableSwap | Oanvänt växlings utrymme (MiB)
PercentAvailableSwap | Oanvänt växlings utrymme i procent av total växling
UsedSwap | Använd växlings utrymme (MiB)
PercentUsedSwap | Använd växlings utrymme i procent av total växling

Den här klassen av mått har bara en enda instans. Attributet "Condition" har inga användbara inställningar och bör utelämnas.

### <a name="builtin-metrics-for-the-network-class"></a>inbyggda mått för nätverks klassen

Nätverks klassen för mått ger information om nätverks aktivitet i ett enskilt nätverks gränssnitt sedan start. LAD visar inte bandbredds mått som kan hämtas från värd mått.

räknare | Innebörd
------- | -------
BytesTransmitted | Totalt antal byte som har skickats sedan start
BytesReceived | Totalt antal mottagna byte sedan start
BytesTotal | Totalt antal byte som skickats eller tagits emot sedan start
PacketsTransmitted | Totalt antal paket som har skickats sedan start
PacketsReceived | Totalt antal mottagna paket sedan start
TotalRxErrors | Antal mottagna fel sedan start
TotalTxErrors | Antal överförings fel sedan start
TotalCollisions | Antal kollisioner som rapporter ATS av nätverks portarna sedan start

 Även om den här klassen är inställd, stöder inte LAD insamlade nätverks mått i alla nätverks enheter. Om du vill hämta måtten för ett speciellt gränssnitt, till exempel eth0, anger du `"condition": "InstanceID=\\"eth0\\""` .

### <a name="builtin-metrics-for-the-filesystem-class"></a>inbyggda mått för klassen fil system

Klassen system för mått innehåller information om fil Systems användning. Absoluta och procentuella värden rapporteras när de visas för en vanlig användare (inte rot).

räknare | Innebörd
------- | -------
LedigtUtrymme | Tillgängligt disk utrymme i byte
UsedSpace | Använt disk utrymme i byte
PercentFreeSpace | Ledigt utrymme i procent
PercentUsedSpace | Använt utrymme i procent
PercentFreeInodes | Procent andel oanvända noder i procent
PercentUsedInodes | Procent andel allokerade (används) noder i procent summeras i alla fil system
BytesReadPerSecond | Lästa byte per sekund
BytesWrittenPerSecond | Skrivna byte per sekund
BytesPerSecond | Lästa byte eller skrivna byte per sekund
ReadsPerSecond | Läs åtgärder per sekund
WritesPerSecond | Skriv åtgärder per sekund
TransfersPerSecond | Läs-eller Skriv åtgärder per sekund

Sammanställda värden för alla fil system kan hämtas genom inställningen `"condition": "IsAggregate=True"` . Värdena för ett bestämt monterat fil system, till exempel "/mnt", kan hämtas genom att ställa in `"condition": 'Name="/mnt"'` . 

> [!NOTE]
> Om du använder Azure Portal i stället för JSON, är rätt villkors fält formulär namn = '/mnt '

### <a name="builtin-metrics-for-the-disk-class"></a>inbyggda mått för disk klassen

Disk klassen för mått innehåller information om disk enhets användning. Den här statistiken gäller hela enheten. Om det finns flera fil system på en enhet, är räknarna för enheten på ett effektivt sätt sammantaget över alla.

räknare | Innebörd
------- | -------
ReadsPerSecond | Läs åtgärder per sekund
WritesPerSecond | Skriv åtgärder per sekund
TransfersPerSecond | Totalt antal åtgärder per sekund
AverageReadTime | Genomsnittligt antal sekunder per Läs åtgärd
AverageWriteTime | Genomsnittligt antal sekunder per Skriv åtgärd
AverageTransferTime | Genomsnittligt antal sekunder per åtgärd
AverageDiskQueueLength | Genomsnittligt antal disk åtgärder i kö
ReadBytesPerSecond | Antal lästa byte per sekund
WriteBytesPerSecond | Antal skrivna byte per sekund
BytesPerSecond | Antal lästa byte eller skrivna per sekund

Sammanställda värden för alla diskar kan hämtas genom att ställa in `"condition": "IsAggregate=True"` . Om du vill hämta information om en speciell enhet (till exempel/dev/sdf1) anger du `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="installing-and-configuring-lad-30"></a>Installera och konfigurera LAD 3,0

### <a name="azure-cli"></a>Azure CLI

Förutsatt att dina skyddade inställningar finns i filen ProtectedSettings.jspå och din offentliga konfigurations information är i PublicSettings.jspå, kör du följande kommando:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Kommandot förutsätter att du använder Azures resurs hanterings läge i Azure CLI. Om du vill konfigurera LAD för virtuella datorer med klassisk distributions modell (ASM) växlar du till "ASM"-läge ( `azure config mode asm` ) och utelämnar resurs gruppens namn i kommandot. Mer information finns i dokumentationen för plattforms [oberoende CLI](/cli/azure/authenticate-azure-cli).

### <a name="powershell"></a>PowerShell

Under förutsättning att dina skyddade inställningar finns i `$protectedSettings` variabeln och din offentliga konfigurations information finns i `$publicSettings` variabeln, kör du följande kommando:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="an-example-lad-30-configuration"></a>Ett exempel på en LAD 3,0-konfiguration

Baserat på föregående definitioner är här ett exempel på en LAD 3,0-tilläggs konfiguration med en förklaring. Om du vill använda det här exemplet i ditt fall bör du använda ditt eget lagrings konto namn, SAS-token för konto och EventHubs SAS-token.

> [!NOTE]
> Beroende på om du använder Azure CLI eller PowerShell för att installera LAD, kommer metoden för att tillhandahålla offentliga och skyddade inställningar att variera. Om du använder Azure CLI sparar du följande inställningar för att ProtectedSettings.jspå och PublicSettings.jspå som ska användas med exempel kommandot ovan. Om du använder PowerShell sparar du inställningarna till `$protectedSettings` och `$publicSettings` genom att köra `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Skyddade inställningar

De här skyddade inställningarna konfigureras:

* ett lagrings konto
* en SAS-token för ett matchande konto
* flera handfat (JsonBlob eller EventHubs med SAS-token)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Offentliga inställningar

Dessa offentliga inställningar gör att LAD:

* Ladda upp måtten för procent-processor-och användnings disk utrymme till `WADMetrics*` tabellen
* Ladda upp meddelanden från syslog-funktionen "User" och allvarlighets grad "info" till `LinuxSyslog*` tabellen
* Ladda upp frågeresultatet för RAW-OMI (PercentProcessorTime och PercentIdleTime) till den namngivna `LinuxCPU` tabellen
* Överför rader som lagts till i filen `/var/log/myladtestlog` till `MyLadTestLog` tabellen

I varje enskilt fall överförs data också till:

* Azure Blob Storage (behållarens namn definieras i JsonBlob-sinken)
* EventHubs-slutpunkt (som anges i EventHubs-mottagaren)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

`resourceId`I konfigurationen måste vara samma som för den virtuella datorn eller skalnings uppsättningen för den virtuella datorn.

* Azures plattforms mått diagram och aviseringar känner till resourceId för den virtuella dator som du arbetar med. Det förväntar sig att hitta data för din virtuella dator med hjälp av Sök nyckeln resourceId.
* Om du använder automatisk skalning i Azure måste resourceId i konfigurationen för automatisk skalning matcha de resourceId som används av LAD.
* ResourceId är inbyggt i namnen på JsonBlobs som skrivits av LAD.

## <a name="view-your-data"></a>Visa dina data

Använd Azure Portal för att visa prestanda data eller ange aviseringar:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Skärm bild som visar Azure Portal med använt disk utrymme på mått som valts och det resulterande diagrammet.":::

`performanceCounters`Data lagras alltid i en Azure Storage tabell. Azure Storage-API: er är tillgängliga för många språk och plattformar.

Data som skickas till JsonBlob-mottagare lagras i blobbar i lagrings kontot med de [skyddade inställningarna](#protected-settings). Du kan använda BLOB-data med hjälp av alla Azure Blob Storage-API: er.

Dessutom kan du använda dessa UI-verktyg för att komma åt data i Azure Storage:

* Visual Studio-Server Explorer.
* [Skärm bild som visar behållare och tabeller i Azure Storage Explorer.](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Den här ögonblicks bilden av en Microsoft Azure Storage Explorer-session visar de genererade Azure Storage tabellerna och behållarna från ett korrekt konfigurerat LAD 3,0-tillägg på en virtuell test dator. Avbildningen stämmer inte exakt med [exemplet på LAD 3,0-konfigurationen](#an-example-lad-30-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Skärm bild som visar Azure Storage Explorer.":::


Se relevant [EventHubs-dokumentation](../../event-hubs/event-hubs-about.md) för att lära dig hur du använder meddelanden som publicerats till en EventHubs-slutpunkt.

## <a name="next-steps"></a>Nästa steg

* Skapa mått varningar i [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md) för de mått som du samlar in.
* Skapa [övervaknings diagram](../../azure-monitor/data-platform.md) för dina mått.
* Lär dig hur du [skapar en skalnings uppsättning för virtuella datorer](../linux/tutorial-create-vmss.md) med hjälp av dina mått för att styra autoskalning.