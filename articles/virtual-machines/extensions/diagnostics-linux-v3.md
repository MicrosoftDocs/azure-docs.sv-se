---
title: Azure Compute – Linux-diagnostiktillägg 3.0
description: Så här konfigurerar du Azure Linux-diagnostiktillägget (LAD) 3.0 för att samla in mått och logghändelser från virtuella Linux-datorer som körs i Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.openlocfilehash: fe03bbfb33f3637eecc4e68f24846c929dad5fa4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479261"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Använda Linux-diagnostiktillägget 3.0 för att övervaka mått och loggar

Det här dokumentet beskriver version 3.0 och senare av Linux-diagnostiktillägget (LAD).

> [!IMPORTANT]
> Information om version 2.3 och tidigare finns i Övervaka prestanda och diagnostikdata för en [virtuell Linux-dator.](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)

## <a name="introduction"></a>Introduktion

Linux-diagnostiktillägget hjälper en användare att övervaka hälsotillståndet för en virtuell Linux-dator som körs Microsoft Azure. Den har följande funktioner:

* Samlar in systemprestandamått från den virtuella datorn och lagrar dem i en specifik tabell i ett anvisat lagringskonto.
* Hämtar logghändelser från syslog och lagrar dem i en specifik tabell i det angivna lagringskontot.
* Gör det möjligt för användare att anpassa de datamått som samlas in och laddas upp.
* Gör det möjligt för användare att anpassa syslog-anläggningar och allvarlighetsnivåerna för händelser som samlas in och laddas upp.
* Gör det möjligt för användare att ladda upp angivna loggfiler till en angiven lagringstabell.
* Stöder sändning av mått och logghändelser till godtyckliga Azure Event Hubs slutpunkter och JSON-formaterade blobar i det avsedda lagringskontot.

Det här tillägget fungerar med båda Azure-distributionsmodellerna.

## <a name="install-the-extension-on-a-vm"></a>Installera tillägget på en virtuell dator

Du kan aktivera tillägget med hjälp Azure PowerShell cmdlets, Azure CLI-skript, Azure resursövervakare-mallar (ARM-mallar) eller Azure Portal. Mer information finns i [Tilläggsfunktioner.](features-linux.md)

>[!NOTE]
>Vissa komponenter i LAD VM-tillägget levereras också i [Log Analytics VM-tillägget](./oms-linux.md). På grund av den här arkitekturen kan konflikter uppstå om båda tilläggen instansieras i samma ARM-mall. 
>
>För att undvika installationskonflikter använder du direktivet för [ `dependsOn` att se](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) till att tilläggen installeras sekventiellt. Tilläggen kan installeras i båda ordningsföljderna.

Dessa installationsanvisningar och en [nedladdningsbar exempelkonfiguration för](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) att konfigurera LAD 3.0 för att:

* Samla in och lagra samma mått som i LAD 2.3.
* Samla in en användbar uppsättning filsystemmått. Den här funktionen är ny i LAD 3.0.
* Avbilda den syslog-standardsamling som LAD 2.3 har aktiverat.
* Aktivera Azure Portal för diagram och aviseringar för VM-mått.

Den nedladdningsbara konfigurationen är bara ett exempel. Ändra den så att den passar dina behov.

### <a name="supported-linux-distributions"></a>Linux-distributioner som stöds

LAD stöder följande distributioner och versioner. Listan över distributioner och versioner gäller endast för Azure-godkända Linux-leverantörsavbildningar. Tillägget stöder vanligtvis inte BYOL- och BYOS-avbildningar från tredje part, t.ex. installationer.

En distribution som endast visar större versioner, t.ex. Debian 7, stöds också för alla mindre versioner. Om en lägre version anges stöds endast den versionen. Om ett plustecken (+) läggs till stöds mindre versioner som är lika med eller senare än den angivna versionen.

Distributioner och versioner som stöds:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Förutsättningar

* **Azure Linux Agent version 2.2.0 eller senare.** De flesta Azure VM Linux-galleriavbildningar innehåller version 2.2.7 eller senare. Kör `/usr/sbin/waagent -version` för att bekräfta vilken version som är installerad på den virtuella datorn. Om den virtuella datorn kör en äldre version uppdaterar [du gästagenten](./update-linux-agent.md).
* **Azure CLI**. Konfigurera Azure [CLI-miljön på datorn om](/cli/azure/install-azure-cli) det behövs.
* Kommandot **wget**. Om du inte redan har det kör du `sudo apt-get install wget` .
* En befintlig **Azure-prenumeration.** 
* Ett befintligt **allmänt lagringskonto** för att lagra data. Allmänna lagringskonton måste ha stöd för tabellagring. Ett Blob Storage-konto fungerar inte.
* **Python 2**.

### <a name="python-requirement"></a>Python-krav

Linux-diagnostiktillägget kräver Python 2. Om den virtuella datorn använder en distribution som inte innehåller Python 2 som standard måste du installera den. Följande exempelkommandon installerar Python 2 på olika distributioner:   

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- Suse: `zypper install -y python2`

Den `python2` körbara filen måste ha aliaset *python*. Här är en metod för att ange det här aliaset:

1. Kör följande kommando för att ta bort alla befintliga alias.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Kör följande kommando för att skapa aliaset.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Exempelinstallation

Exempelkonfigurationen som laddas ned i följande exempel samlar in en uppsättning standarddata och skickar dem till tabellagring. URL:en för exempelkonfigurationen och dess innehåll kan ändras. 

I de flesta fall bör du ladda ned en kopia av JSON-filen med portalinställningar och anpassa den efter dina behov. Använd sedan mallar eller din egen automatisering för att använda en anpassad version av konfigurationsfilen i stället för att ladda ned från URL:en varje gång.

> [!NOTE]
> För följande exempel fyller du i rätt värden för variablerna i det första avsnittet innan du kör koden. 
#### <a name="azure-cli-sample"></a>Azure CLI-exempel

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>Azure CLI-exempel för att installera LAD 3.0 på VM-skalningsuppsättningsinstansen

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
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

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>Uppdatera tilläggsinställningarna

När du har ändrat dina skyddade eller offentliga inställningar distribuerar du dem till den virtuella datorn genom att köra samma kommando. Om inställningarna ändras skickas uppdateringarna till tillägget. LAD läser in konfigurationen på nytt och startar om sig själv.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migrera från tidigare versioner av tillägget

Den senaste versionen av tillägget är *4.0*. 

> [!IMPORTANT]
> Det här tillägget introducerar större ändringar i konfigurationen. En sådan ändring förbättrade säkerheten för tillägget, så bakåtkompatibiliteten med 2.x kunde inte upprätthållas. Dessutom skiljer sig tilläggsutgivaren för det här tillägget från utgivaren för 2.x-versionerna.
>
> Om du vill migrera från 2.x till den nya versionen avinstallerar du först det gamla tillägget (under det gamla utgivarnamnet). Installera sedan version 3.

Rekommendationer:

* Installera tillägget med automatisk uppgradering av delversionen aktiverad.
  * På virtuella datorer med den klassiska distributionsmodellen anger du version om du installerar `3.*` tillägget via Azure XPLAT CLI eller PowerShell.
  * Inkludera Azure Resource Manager vm-distributionsmallen på den virtuella datorn `"autoUpgradeMinorVersion": true` i distributionsmodellen.
* Använd ett nytt eller annat lagringskonto för LAD 3.0. LAD 2.3 och LAD 3.0 har flera små inkompatibiliteter som gör det svårt att dela ett konto:
  * LAD 3.0 lagrar syslog-händelser i en tabell som har ett annat namn.
  * Strängarna `counterSpecifier` för mått skiljer sig åt i `builtin` LAD 3.0.

## <a name="protected-settings"></a>Skyddade inställningar

Den här uppsättningen konfigurationsinformation innehåller känslig information som ska skyddas från offentlig vy. Den innehåller till exempel autentiseringsuppgifter för lagring. De här inställningarna överförs till och lagras av tillägget i krypterad form.

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
storageAccountName | Namnet på lagringskontot där data skrivs av tillägget.
storageAccountEndPoint | (Valfritt) Slutpunkten som identifierar molnet där lagringskontot finns. Om den här inställningen saknas är standardvärdet för LAD Det offentliga Azure-molnet, `https://core.windows.net` . Om du vill använda ett lagringskonto i Azure Azure Government eller Azure China 21Vianet anger du det här värdet som obligatoriskt.
storageAccountSasToken | En [KONTO-SAS-token](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) för blob- och tabelltjänster ( `ss='bt'` ). Det gäller för containrar och objekt ( `srt='co'` ). Den ger behörighet att lägga till, skapa, lista, uppdatera och skriva ( `sp='acluw'` ). Ta *inte* med inledande frågetecken (?).
mdsdHttpProxy | (Valfritt) HTTP-proxyinformation som tillägget behöver för att ansluta till det angivna lagringskontot och slutpunkten.
sinksConfig | (Valfritt) Information om alternativa mål som mått och händelser kan levereras till. I följande avsnitt beskrivs information om varje data mottagare som stöds av tillägget.

Om du vill hämta en SAS-token i en ARM-mall använder du `listAccountSas` funktionen . En exempelmall finns i List [function example](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Du kan skapa den SAS-token som krävs via Azure Portal:

1. Välj det allmänna lagringskonto som du vill att tillägget ska skriva till.
1. I menyn till vänster under Inställningar **väljer du** Signatur för **delad åtkomst.**
1. Gör valen enligt beskrivningen ovan.
1. Välj **Generera SAS.**

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Skärmbild som visar sidan Signatur för delad åtkomst med knappen Generera S A S.":::

Kopiera den genererade SAS:en till `storageAccountSasToken` fältet . Ta bort inledande frågetecken (?).

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

Det `sinksConfig` valfria avsnittet definierar fler mål som tillägget skickar den information som det samlar in. Matrisen `sink` innehåller ett -objekt för varje ytterligare data mottagare. Attributet `type` avgör de andra attributen i objektet.

Element | Värde
------- | -----
name | En sträng som refererar till den här mottagaren någon annanstans i tilläggskonfigurationen.
typ | Den typ av mottagare som definieras. Anger de andra värdena (om sådana finns) i instanser av den här typen.

LAD version 3.0 stöder två typer av mottagare: `EventHub` och `JsonBlob` .

#### <a name="eventhub-sink"></a>EventHub-mottagare

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

Posten `"sasURL"` innehåller den fullständiga URL:en, inklusive SAS-token, för den händelsehubb som data ska publiceras till. LAD kräver en SAS för att namnge en princip som gör det möjligt att skicka anspråket. 

Exempel:

* Skapa ett Azure Event Hubs namnområde med namnet `contosohub` .
* Skapa en händelsehubb i namnområdet `syslogmsgs` .
* Skapa en princip för delad åtkomst på händelsehubben som gör det möjligt att skicka anspråket. Ge principen namnet `writer` .

Om din SAS är bra fram till midnatt UTC den 1 januari 2018 kan sasURL-värdet se ut så här:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Mer information om hur du genererar och hämtar information om SAS-token för Event Hubs finns i [Generera en SAS-token](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="jsonblob-sink"></a>JsonBlob-mottagare

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data som dirigeras `JsonBlob` till en mottagare lagras i blobar i Azure Storage. Varje instans av LAD skapar en blob varje timme för varje mottagares namn. Varje blob innehåller alltid en syntaktiskt giltig JSON-matris med objekt. Nya poster läggs till atomiskt i matrisen. 

Blobar lagras i en container som har samma namn som mottagaren. Reglerna Azure Storage för blobcontainernamn gäller för namnen `JsonBlob` på mottagare. Namnet måste innehålla mellan 3 och 63 gemener alfanumeriska ASCII-tecken eller bindestreck.

## <a name="public-settings"></a>Offentliga inställningar

Den offentliga inställningsstrukturen innehåller olika inställningsblock som styr den information som tillägget samlar in. Varje inställning är valfri. Om du anger `ladCfg` måste du också ange `StorageAccount` .

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
StorageAccount | Namnet på lagringskontot där data skrivs av tillägget. Det måste vara det namn som anges i [de skyddade inställningarna](#protected-settings).
mdsdHttpProxy | (Valfritt) Samma som i de [skyddade inställningarna](#protected-settings). Det offentliga värdet åsidosätts av det privata värdet, om det har angetts. Placera proxyinställningar som innehåller en hemlighet, till exempel ett lösenord, i de [skyddade inställningarna](#protected-settings).

Följande avsnitt innehåller information om de återstående elementen.

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

Strukturen `ladCfg` är valfri. Den styr insamlingen av mått och loggar som levereras till Azure Monitor Metrics-tjänsten och till andra data mottagare. Du måste ange:

* Antingen `performanceCounters` `syslogEvents` eller eller båda. 
* `metrics`Strukturen.

Element | Värde
------- | -----
eventVolume | (Valfritt) Styr antalet partitioner som skapas i lagringstabellen. Det måste vara `"Large"` `"Medium"` , eller `"Small"` . Om inget värde anges är standardvärdet `"Medium"` .
sampleRateInSeconds | (Valfritt) Standardintervallet mellan insamlingen av råa (ej aggregerade) mått. Den minsta samplingsfrekvensen som stöds är 15 sekunder. Om värdet inte anges är standardvärdet `15` .

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
resourceId | Det Azure Resource Manager resurs-ID:t för den virtuella datorn eller skalningsuppsättningen som den virtuella datorn tillhör. Den här inställningen måste också anges om `JsonBlob` någon mottagare används i konfigurationen.
scheduledTransferPeriod | Den frekvens med vilken aggregeringsmått beräknas och överförs Azure Monitor mått. Frekvensen uttrycks som ett IS 8601-tidsintervall. Den minsta överföringsperioden är 60 sekunder, det vill säga PT1M. Ange minst en `scheduledTransferPeriod` .

Exempel på de mått som anges i avsnittet samlas in var 15:e sekund eller med den `performanceCounters` samplingsfrekvens som uttryckligen definierats för räknaren. Om flera `scheduledTransferPeriod` frekvenser visas, som i exemplet, beräknas varje aggregering oberoende av varandra.

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

Det `performanceCounters` valfria avsnittet styr insamlingen av mått. Rådata sammanställs för var och en [`scheduledTransferPeriod`](#metrics) för att skapa dessa värden:

* Medelvärde
* Minimum
* Maximal
* Senast insamlade värden
* Antal råexempel som används för att beräkna aggregeringen

Element | Värde
------- | -----
Sjunker | (Valfritt) En kommaavgränsad lista med namn på mottagare som LAD skickar aggregerade måttresultat till. Alla aggregerade mått publiceras till varje listad mottagare. Exempel: `"EHsink1, myjsonsink"`. Mer information finns i [`sinksConfig`](#sinksconfig).
typ | Identifierar den faktiska providern av måttet.
klass | Tillsammans med `"counter"` identifierar det specifika måttet i providerns namnområde.
räknare | Tillsammans med `"class"` identifierar det specifika måttet inom leverantörens namnområde.
counterSpecifier | Identifierar det specifika måttet i Azure Monitor metrics-namnområdet.
Villkor | (Valfritt) Väljer en specifik instans av objektet som måttet gäller för. Eller så väljer den aggregeringen över alla instanser av objektet. 
Samplerate | IS 8601-intervallet som anger med vilken hastighet råprover för det här måttet samlas in. Om värdet inte anges anges samlingsintervallet med värdet [`sampleRateInSeconds`](#ladcfg) . Den kortaste samplingsfrekvensen som stöds är 15 sekunder (PT15S).
unit | Definierar måttenheten. Bör vara någon av dessa strängar: `"Count"` , , , , , , `"Bytes"` `"Seconds"` `"Percent"` `"CountPerSecond"` `"BytesPerSecond"` `"Millisecond"` . Konsumenter av insamlade data förväntar sig att insamlade datavärden matchar den här enheten. LAD ignorerar det här fältet.
displayName | Etiketten som ska kopplas till data i Azure Monitor Mått. Den här etiketten är på det språk som anges av den associerade språkinställningen. LAD ignorerar det här fältet.

är `counterSpecifier` en godtycklig identifierare. Konsumenter av mått, till exempel Azure Portal och aviseringsfunktionen, använder som "nyckel" som identifierar ett mått eller en `counterSpecifier` instans av ett mått. 

För `builtin` mått rekommenderar vi värden som börjar med `counterSpecifier` `/builtin/` . Om du samlar in en specifik instans av ett mått rekommenderar vi att du kopplar instansens identifierare till `counterSpecifier` värdet. 

Här är några exempel:

* `/builtin/Processor/PercentIdleTime` – Inaktivitetstid i genomsnitt för alla virtuella processorer
* `/builtin/Disk/FreeSpace(/mnt)` – Ledigt utrymme för `/mnt` filsystemet
* `/builtin/Disk/FreeSpace` – Ledigt utrymme i genomsnitt för alla monterade filsystem

LAD och Azure Portal förväntar sig inte att `counterSpecifier` värdet matchar något mönster. Var konsekvent i hur du skapar `counterSpecifier` värden.

När du anger `performanceCounters` skriver LAD alltid data till en tabell i Azure Storage. Samma data kan skrivas till JSON-blobar eller Event Hubs eller båda. Men du kan inte inaktivera lagring av data i en tabell. 

Alla instanser av LAD som använder samma lagringskontonamn och slutpunkt lägger till sina mått och loggar i samma tabell. Om för många virtuella datorer skriver till samma tabellpartition kan Azure begränsa skrivningar till den partitionen. 

Inställningen `eventVolume` gör att poster sprids över 1 (liten), 10 (medel) eller 100 (stora) partitioner. Vanligtvis räcker det med medelstora partitioner för att undvika trafikbegränsning. 

Funktionen Azure Monitor mått i den här Azure Portal använder data i den här tabellen för att skapa grafer eller utlösa aviseringar. Tabellnamnet är sammanfogningen av dessa strängar:

* `WADMetrics`
* `"scheduledTransferPeriod"`för aggregerade värden som lagras i tabellen
* `P10DV2S`
* Ett datum i formuläret "YYYYMMDD" som ändras var 10:e dag

Exempel är `WADMetricsPT1HP10DV2S20170410` och `WADMetricsPT1MP10DV2S20170609` .

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

Det `syslogEvents` valfria avsnittet styr insamlingen av logghändelser från syslog. Om avsnittet utelämnas avbildas inte syslog-händelser alls.

Samlingen `syslogEventConfiguration` har en post för varje syslog-anläggning av intresse. Om är för en viss anläggning, eller om den anläggningen inte visas i elementet alls, fångas inga händelser från `minSeverity` `"NONE"` den anläggningen.

Element | Värde
------- | -----
Sjunker | En kommaavgränsad lista med namn på mottagare som enskilda logghändelser publiceras till. Alla logghändelser som matchar begränsningarna i `syslogEventConfiguration` publiceras till varje mottagare i listan. Exempel: `"EHforsyslog"`
facilityName | Ett syslog-anläggningsnamn, till exempel `"LOG_USER"` eller `"LOG\LOCAL0"` . Mer information finns i avsnittet "Anläggning" på [syslog-man-sidan.](http://man7.org/linux/man-pages/man3/syslog.3.html)
minSeverity | En allvarlighetsgrad för syslog, till exempel `"LOG_ERR"` eller `"LOG_INFO"` . Mer information finns i avsnittet "Nivå" på [syslog man-sidan.](http://man7.org/linux/man-pages/man3/syslog.3.html) Tillägget samlar in händelser som skickas till anläggningen på eller över den angivna nivån.

När du anger `syslogEvents` skriver LAD alltid data till en tabell i Azure Storage. Samma data kan skrivas till JSON-blobar eller Event Hubs eller båda. Men du kan inte inaktivera lagring av data i en tabell. 

Partitioneringsbeteendet för tabellen är detsamma som det som beskrivs för `performanceCounters` . Tabellnamnet är sammanfogningen av dessa strängar:

* `LinuxSyslog`
* Ett datum i formuläret "YYYYMMDD" som ändras var 10:e dag

Exempel är `LinuxSyslog20170410` och `LinuxSyslog20170609` .

### <a name="perfcfg"></a>perfCfg

Avsnittet `perfCfg` är valfritt. Den styr körningen av [godtyckliga OMI-frågor (Open Management Infrastructure).](https://github.com/Microsoft/omi)

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
namnområde | (Valfritt) Det OMI-namnområde där frågan ska köras. Om det inte anges är standardvärdet `"root/scx"` . Den implementeras av de [System Center plattformsoberoende leverantörerna](https://github.com/Microsoft/SCXcore).
DocumentDB | OMI-frågan som ska köras.
tabell | (Valfritt) Den Azure Storage tabellen i det avsedda lagringskontot. Mer information finns i [Skyddade inställningar.](#protected-settings)
frequency | (Valfritt) Antalet sekunder mellan frågekörningar. Standardvärdet är 300 (5 minuter). Det minsta värdet är 15 sekunder.
Sjunker | (Valfritt) En kommaavgränsad lista med namn på fler mottagare till vilka råa exempelmåttresultat ska publiceras. Ingen aggregering av dessa råexempel beräknas av tillägget eller Azure Monitor Mått.

Antingen `"table"` eller eller båda måste `"sinks"` anges.

### <a name="filelogs"></a>fileLogs

Avsnittet `fileLogs` styr insamlingen av loggfiler. LAD samlar in nya textrader när de skrivs till filen. Den skriver dem till tabellrader och/eller angivna mottagare ( `JsonBlob` eller `EventHub` ).

> [!NOTE]
> `fileLogs`avbildas av en delkomponent av LAD som kallas `omsagent` . Om du `fileLogs` vill samla in ser du till att användaren har `omsagent` läsbehörighet för de filer som du anger. Användaren måste också ha körbehörighet för alla kataloger i sökvägen till filen. När LAD har installerats kan du kontrollera behörigheterna genom att köra `sudo su omsagent -c 'cat /path/to/file'` .

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
file | Den fullständiga sökvägen till loggfilen som ska övervakas och avbildas. Sökvägsnamnet måste ge en enskild fil ett namn. Den kan inte namnge en katalog eller innehålla jokertecken. Användarkontot `omsagent` måste ha läsbehörighet till filsökvägen.
tabell | (Valfritt) Den Azure Storage tabell där nya rader från "slutet" av filen skrivs. Tabellen måste finnas i det angivna lagringskontot, enligt vad som anges i den skyddade konfigurationen. 
Sjunker | (Valfritt) En kommaavgränsad lista med namn på fler mottagare som loggrader skickas till.

Antingen `"table"` `"sinks"` eller , eller båda, måste anges.

## <a name="metrics-supported-by-the-builtin-provider"></a>Mått som stöds av den inbyggda providern

Måttleverantören `builtin` är en källa till mått som är mest intressanta för en bred uppsättning användare. De här måtten är indela i fem breda klasser:

* Processor
* Minne
* Nätverk
* Filsystem
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>inbyggda mått för processorklassen

Måttklassen Processor innehåller information om processoranvändningen på den virtuella datorn. När procentandelar aggregeras är resultatet medelvärdet för alla processorer. 

Om en virtuell dator med två vCPU:er är 100 procent upptagen och den andra är 100 procent inaktiv är den rapporterade `PercentIdleTime` 50. Om varje vCPU är 50 procent upptagen under samma period är det rapporterade resultatet också 50. När en virtuell dator med fyra virtuella processor är 100 procent upptagen och de andra är inaktiva är den rapporterade `PercentIdleTime` 75.

Räknare | Innebörd
------- | -------
PercentIdleTime | Procentandel tid under aggregeringsfönstret som processorerna körde kernel-inaktivitetsloopen
PercentProcessorTime | Procentandel tid som kör en icke-inaktiv tråd
PercentIOWaitTime | Procentandel av tiden som väntar på att I/A-åtgärder ska slutföras
PercentInterruptTime | Procentandel tidsavbrott för maskinvara eller programvara och DPC (uppskjutna procedursamtal)
PercentUserTime | Av icke-inaktiv tid under aggregeringsfönstret, procentandelen av tiden som ägnats i användarläge med normal prioritet
PercentNiceTime | Av icke-inaktiv tid har procentandelen som spenderats på lägre (bra) prioritet
PercentPrivilegedTime | Av icke-inaktiv tid spenderas procentandelen i privilegierat läge (kernel)

De första fyra räknarna ska summeras till 100 procent. De sista tre räknarna summeras också till 100 procent. Dessa tre räknare delar in summan av `PercentProcessorTime` , `PercentIOWaitTime` och `PercentInterruptTime` .

Om du vill aggregera ett enda mått för alla processorer anger du `"condition": "IsAggregate=TRUE"` . Om du vill hämta ett mått för en specifik processor, till exempel den andra logiska processorn för en virtuell dator med fyra virtuella processorer, anger du `"condition": "Name=\\"1\\""` . Logiska processornummer ligger i intervallet `[0..n-1]` .

### <a name="builtin-metrics-for-the-memory-class"></a>inbyggda mått för klassen Memory

Måttklassen Minne innehåller information om minnesanvändning, växling och växling.

Räknare | Innebörd
------- | -------
AvailableMemory | Tillgängligt fysiskt minne i MiB
PercentAvailableMemory | Tillgängligt fysiskt minne som en procentandel av det totala minnet
UsedMemory | Fysiskt minne som används (MiB)
PercentUsedMemory | Fysiskt minne används som en procentandel av det totala minnet
PagesPerSec | Totalt antal sidinläsningar (läsning/skrivning)
PagesReadPerSec | Sidor som läses från lagringslagret, till exempel växlingsfil, programfil och mappad fil
PagesWrittenPerSec | Sidor som skrivits till lagringslagret, till exempel växlingsfil och mappad fil
AvailableSwap | Oanvänd växlingsutrymme (MiB)
PercentAvailableSwap | Oanvänd växlingsutrymme som en procentandel av den totala växlingen
UsedSwap | Växlingsutrymme under användning (MiB)
PercentUsedSwap | Växlingsutrymme under användning som en procentandel av den totala växlingen

Den här måttklassen har bara en instans. Attributet `"condition"` har inga användbara inställningar och bör utelämnas.

### <a name="builtin-metrics-for-the-network-class"></a>inbyggda mått för klassen Network

Måttklassen Nätverk innehåller information om nätverksaktivitet i ett enskilt nätverksgränssnitt sedan starten. 

LAD exponerar inte bandbreddsmått. Du kan hämta dessa mått från värdmått.

Räknare | Innebörd
------- | -------
Byte har skickats | Totalt antal byte som skickats sedan starten
ByteReceived | Totalt antal byte som tagits emot sedan starten
BytesTotal | Totalt antal byte som skickats eller tagits emot sedan starten
Paket som haröversatts | Totalt antal paket som skickats sedan starten
PaketReceived | Totalt antal paket som tagits emot sedan starten
TotalRxErrors | Antal mottagningsfel sedan starten
TotalTxErrors | Antal överföringsfel sedan starten
TotalCollisions | Antal kollisioner som rapporterats av nätverksportarna sedan starten

Även om klassen Nätverk har instans, stöder LAD inte insamling av nätverksmått aggregerade över alla nätverksenheter. Om du vill hämta måtten för ett specifikt gränssnitt, till exempel eth0, anger du `"condition": "InstanceID=\\"eth0\\""` .

### <a name="builtin-metrics-for-the-file-system-class"></a>inbyggda mått för filsystemklassen

Måttklassen Filsystem innehåller information om filsystemets användning. Absoluta värden och procentvärden rapporteras som de skulle visas för en vanlig användare (inte rot).

Räknare | Innebörd
------- | -------
LedigtUtrymme | Tillgängligt diskutrymme i byte
UsedSpace | Använt diskutrymme i byte
PercentFreeSpace | Ledigt utrymme i procent
PercentUsedSpace | Procent använt utrymme
PercentFreeInodes | Procentandel oanvända indexnoder (i noder)
PercentUsedInodes | Procentandel allokerade (används) i -noder summerade i alla filsystem
ByteReadPerSecond | Byte läses per sekund
BytesWrittenPerSecond | Byte skrivna per sekund
BytePerSecond | Byte som läses eller skrivs per sekund
ReadsPerSecond | Läsåtgärder per sekund
WritesPerSecond | Skrivåtgärder per sekund
TransfersPerSecond | Läs- eller skrivåtgärder per sekund

Du kan hämta aggregerade värden i alla filsystem genom att ange `"condition": "IsAggregate=True"` . Hämta värden för ett specifikt monterat filsystem, till exempel `"/mnt"` , genom att ange `"condition": 'Name="/mnt"'` . 

> [!NOTE]
> Om du arbetar i Azure Portal I stället för JSON är villkorsfältformuläret `Name='/mnt'` .

### <a name="builtin-metrics-for-the-disk-class"></a>inbyggda mått för diskklassen

Måttklassen Disk innehåller information om diskenhetsanvändning. Den här statistiken gäller för hela enheten. 

När en enhet har flera filsystem aggregeras räknarna för den enheten effektivt i alla filsystem.

Räknare | Innebörd
------- | -------
ReadsPerSecond | Läsåtgärder per sekund
WritesPerSecond | Skrivåtgärder per sekund
TransfersPerSecond | Totalt antal åtgärder per sekund
AverageReadTime | Genomsnittliga sekunder per läsåtgärd
AverageWriteTime | Genomsnittliga sekunder per skrivåtgärd
AverageTransferTime | Genomsnittliga sekunder per åtgärd
AverageDiskQueueLength | Genomsnittligt antal diskåtgärder i kö
ReadBytesPerSecond | Antal lästa byte per sekund
WriteBytesPerSecond | Antal skrivna byte per sekund
BytePerSecond | Antal lästa eller skrivna byte per sekund

Du kan hämta aggregerade värden för alla diskar genom att ange `"condition": "IsAggregate=True"` . Om du vill hämta information för en specifik enhet (till exempel `/dev/sdf1` ) anger du `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="install-and-configure-lad-30"></a>Installera och konfigurera LAD 3.0

### <a name="azure-cli"></a>Azure CLI

Om dina skyddade inställningar finns i *filenProtectedSettings.jspå* och din offentliga konfigurationsinformation finns iPublicSettings.js *på* kör du följande kommando.

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Kommandot förutsätter att du använder Azure Resource Manager läge i Azure CLI. Om du vill konfigurera LAD för den klassiska distributionsmodellens virtuella datorer växlar du till "asm"-läge ( ) och utelämnar `azure config mode asm` resursgruppens namn i kommandot . 

Mer information finns i [CLI-dokumentationen för flera plattformar.](/cli/azure/authenticate-azure-cli)

### <a name="powershell"></a>PowerShell

Om dina skyddade inställningar finns i `$protectedSettings` variabeln och din offentliga konfigurationsinformation finns i `$publicSettings` variabeln kör du det här kommandot:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>Exempelkonfiguration för LAD 3.0

Det här avsnittet innehåller ett exempel på konfiguration av LAD 3.0-tillägg och en förklaring baserat på föregående definitioner. Om du vill använda det här exemplet i ditt fall använder du ditt eget lagringskontonamn, kontots SAS-token och Event Hubs SAS-token.

> [!NOTE]
> Beroende på om du använder Azure CLI eller PowerShell för att installera LAD, skiljer sig metoden för att tillhandahålla offentliga och skyddade inställningar: 
>
> * Om du använder Azure CLI sparar du följande inställningar för *attProtectedSettings.jspå* och *PublicSettings.js* på för att använda föregående exempelkommando. 
> * Om du använder PowerShell sparar du följande inställningar i och `$protectedSettings` `$publicSettings` genom att köra `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Skyddade inställningar

De skyddade inställningarna konfigurerar:

* Ett lagringskonto.
* En matchande KONTO-SAS-token.
* Flera mottagare ( eller `JsonBlob` `EventHub` med SAS-token).

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

De offentliga inställningarna gör att LAD:

* Ladda upp mätvärden för procentprocessortid och mått för använt diskutrymme till `WADMetrics*` tabellen.
* Ladda upp meddelanden från `"user"` systemloggen och `"info"` allvarlighetsgraden till `LinuxSyslog*` tabellen.
* Ladda upp råa OMI-frågeresultat ( `PercentProcessorTime` och ) till den `PercentIdleTime` namngivna `LinuxCPU` tabellen.
* Ladda upp tilladdade rader i `/var/log/myladtestlog` filen till `MyLadTestLog` tabellen.

I varje fall överförs även data till:

* Azure Blob Storage. Containernamnet är som det definieras i `JsonBlob` mottagaren.
* Den Event Hubs slutpunkt som anges i `EventHub` mottagaren.

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

i `resourceId` konfigurationen måste matcha den virtuella datorns eller den virtuella datorns skalningsuppsättning.

* Azure-plattformens måttdiagram och aviseringar känner `resourceId` till vilken virtuell dator du arbetar med. Den förväntar sig att hitta data för den virtuella datorn med `resourceId` hjälp av uppslagsnyckeln.
* Om du använder Azure Autoscale måste `resourceId` i konfigurationen för autoskalning matcha `resourceId` den som används av LAD.
* `resourceId`är inbyggt i namnen på JSON-blobar som skrivits av LAD.

## <a name="view-your-data"></a>Visa dina data

Använd Azure Portal för att visa prestandadata eller ställa in aviseringar:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Skärmbild som visar Azure Portal. Måttet Använt diskutrymme har valts. Det resulterande diagrammet visas.":::

Data `performanceCounters` lagras alltid i en Azure Storage tabell. Azure Storage API:er är tillgängliga för många språk och plattformar.

Data som skickas `JsonBlob` till mottagare lagras i blobar i lagringskontot med namnet i [de skyddade inställningarna](#protected-settings). Du kan använda blobdata med valfria Azure Blob Storage API:er.

Du kan också använda dessa gränssnittsverktyg för att komma åt data i Azure Storage:

* Visual Studio Server Explorer
* [Azure Lagringsutforskaren](https://azurestorageexplorer.codeplex.com/)

Följande skärmbild av en Azure Storage Explorer-session visar de genererade Azure Storage tabeller och containrar från ett korrekt konfigurerat LAD 3.0-tillägg på en virtuell test-dator. Avbildningen matchar inte exakt [LAD.0-exempelkonfigurationen](#example-lad-30-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Skärmbild som visar Azure Storage Explorer.":::


Mer information om hur du använder meddelanden som publicerats till en Event Hubs-slutpunkt finns i relevant [Event Hubs dokumentation](../../event-hubs/event-hubs-about.md).

## <a name="next-steps"></a>Nästa steg

* I [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md)skapar du aviseringar för de mått som du samlar in.
* Skapa [övervakningsdiagram](../../azure-monitor/data-platform.md) för dina mått.
* [Skapa en VM-skalningsuppsättning](../linux/tutorial-create-vmss.md) med hjälp av dina mått för att styra autoskalning.
