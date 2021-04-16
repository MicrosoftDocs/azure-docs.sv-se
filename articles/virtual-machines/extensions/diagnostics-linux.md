---
title: Azure Compute – Linux-diagnostiktillägg 4.0
description: Så här konfigurerar du Azure Linux-diagnostiktillägget (LAD) 4.0 för att samla in mått och logghändelser från virtuella Linux-datorer som körs i Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/05/2021
ms.openlocfilehash: 2e862915bcc524db50e7e66c969b713f729c64aa
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479652"
---
# <a name="use-the-linux-diagnostic-extension-40-to-monitor-metrics-and-logs"></a>Använd Linux-diagnostiktillägget 4.0 för att övervaka mått och loggar

Det här dokumentet beskriver de senaste versionerna av Linux-diagnostiktillägget (LAD).

> [!IMPORTANT]
> Information om version 3.x finns i [Använda Linux-diagnostiktillägget 3.0 för att övervaka mått och loggar.](./diagnostics-linux-v3.md) Information om version 2.3 och tidigare finns i Övervaka prestanda och diagnostikdata för en [virtuell Linux-dator.](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)

## <a name="introduction"></a>Introduktion

Linux-diagnostiktillägget hjälper en användare att övervaka hälsotillståndet för en virtuell Linux-dator som körs Microsoft Azure. Den har följande funktioner:

* Samlar in systemprestandamått från den virtuella datorn och lagrar dem i en specifik tabell i ett anvisat lagringskonto.
* Hämtar logghändelser från syslog och lagrar dem i en specifik tabell i det angivna lagringskontot.
* Gör det möjligt för användare att anpassa de datamått som samlas in och laddas upp.
* Gör det möjligt för användare att anpassa syslog-anläggningar och allvarlighetsnivåerna för händelser som samlas in och laddas upp.
* Gör det möjligt för användare att ladda upp angivna loggfiler till en angiven lagringstabell.
* Stöder sändning av mått och logghändelser till godtyckliga EventHub-slutpunkter och JSON-formaterade blobar i det avsedda lagringskontot.

Det här tillägget fungerar med båda Azure-distributionsmodellerna.

## <a name="install-the-extension-on-a-vm"></a>Installera tillägget på en virtuell dator

Du kan aktivera det här tillägget med hjälp Azure PowerShell cmdlets, Azure CLI-skript, Azure Resource Manager-mallar (ARM-mallar) eller Azure Portal. Mer information finns i [Tillägg och funktioner.](features-linux.md)

>[!NOTE]
>Vissa komponenter i linux-diagnostiktillägget för virtuella datorer levereras också i [Log Analytics VM-tillägget](./oms-linux.md). På grund av den här arkitekturen kan konflikter uppstå om båda tilläggen instansieras i samma ARM-mall. 
>
>Använd -direktivet för att installera tilläggen [ `dependsOn` sekventiellt för](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) att undvika installationskonflikter. Tilläggen kan installeras i båda ordningsföljderna.

Använd installationsanvisningarna och en nedladdningsbar [exempelkonfiguration för att](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) konfigurera LAD 4.0 för att:

* Samla in och lagra samma mått som LAD-versionerna 2.3 och 3.x.
* Skicka mått till Azure Monitor tillsammans med den vanliga mottagaren till Azure Storage. Den här funktionen är ny i LAD 4.0.
* Samla in en användbar uppsättning filsystemmått, som i LAD 3.0.
* Avbilda syslog-standardsamlingen som aktiverats av LAD 2.3.
* Aktivera Azure Portal för diagram och aviseringar för VM-mått.

Den nedladdningsbara konfigurationen är bara ett exempel. Ändra den så att den passar dina behov.

### <a name="supported-linux-distributions"></a>Linux-distributioner som stöds

Linux-diagnostiktillägget stöder många distributioner och versioner. Följande lista över distributioner och versioner gäller endast för Azure-godkända Linux-leverantörsavbildningar. Tillägget stöder vanligtvis inte BYOL- och BYOS-avbildningar från tredje part, t.ex. installationer.

En distribution som endast visar större versioner, t.ex. Debian 7, stöds också för alla mindre versioner. Om en specifik delversion anges stöds endast den versionen. Om ett plustecken (+) läggs till stöds mindre versioner som är lika med eller senare än den angivna versionen.

Distributioner och versioner som stöds:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Förutsättningar

* **Azure Linux-agent version 2.2.0 eller senare.** De flesta Azure VM Linux-galleriavbildningar innehåller version 2.2.7 eller senare. Kör `/usr/sbin/waagent -version` för att bekräfta vilken version som är installerad på den virtuella datorn. Om den virtuella datorn kör en äldre version av gästagenten uppdaterar [du Linux-agenten](./update-linux-agent.md).
* **Azure CLI**. [Konfigurera Azure CLI-miljön](/cli/azure/install-azure-cli) på datorn.
* **Kommandot `wget`**. Om du inte redan har det kör du `sudo apt-get install wget` .
* **En Azure-prenumeration och ett allmänt lagringskonto** för lagring av data.  Allmänna lagringskonton har stöd för tabellagring, vilket krävs.  Ett Blob Storage-konto fungerar inte.
* **Python 2**.

### <a name="python-requirement"></a>Python-krav

Linux-diagnostiktillägget kräver Python 2. Om den virtuella datorn använder en distribution som inte innehåller Python 2 som standard installerar du den. 

Följande exempelkommandon installerar Python 2 på olika distributioner:    

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- Suse: `zypper install -y python2`

Den `python2` körbara filen måste ha aliaset *python*. Här är ett sätt att ange det här aliaset:

1. Kör följande kommando för att ta bort alla befintliga alias.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Kör följande kommando för att skapa aliaset.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Exempelinstallation

> [!NOTE]
> För följande exempel fyller du i rätt värden för variablerna i det första avsnittet innan du kör koden. 

I de här exemplen samlar exempelkonfigurationen in en uppsättning standarddata och skickar dem till tabellagring. URL:en för exempelkonfigurationen och dess innehåll kan ändras. 

I de flesta fall bör du ladda ned en kopia av JSON-filen med portalinställningar och anpassa den efter dina behov. Använd sedan mallar eller din egen automatisering för att använda en anpassad version av konfigurationsfilen i stället för att ladda ned från URL:en varje gång.

> [!NOTE]
> När du aktiverar den nya Azure Monitor-mottagaren måste de virtuella datorerna ha system tilldelad identitet aktiverad för att generera autentiseringstoken för hanterad tjänstidentitet (MSI). Du kan lägga till de här inställningarna under eller efter skapandet av den virtuella datorn. 
>
> Anvisningar för Azure Portal, Azure CLI, PowerShell och Azure Resource Manager finns i [Konfigurera hanterade identiteter.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) 



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

# Enable system-assigned identity on the existing VM.
az vm identity assign -g $my_resource_group -n $my_linux_vm

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
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-40-on-a-virtual-machine-scale-set-instance"></a>Azure CLI-exempel för att installera LAD 4.0 på en vm-skalningsuppsättningsinstans

```azurecli
# Set your Azure virtual machine scale set diagnostic variables. 
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Enable system-assigned identity on the existing virtual machine scale set.
az vmss identity assign -g $my_resource_group -n $my_linux_vmss

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
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell-exempel

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Enable system-assigned identity on an existing VM
Update-AzVM -ResourceGroupName $VMresourceGroup -VM $vm -IdentityType SystemAssigned

# Get the public settings template from GitHub and update the templated values for the storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0 
```

### <a name="update-the-extension-settings"></a>Uppdatera tilläggsinställningarna

När du har ändrat dina skyddade eller offentliga inställningar distribuerar du dem till den virtuella datorn genom att köra samma kommando. Om några inställningar ändras skickas uppdateringarna till tillägget. LAD läser in konfigurationen på nytt och startar om sig själv.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migrera från tidigare versioner av tillägget

Den senaste versionen av tillägget är *4.0, som för närvarande är i offentlig förhandsversion.* Äldre versioner av 3.x stöds fortfarande. Men 2.x-versioner har gjorts inaktuella sedan den 31 juli 2018.

> [!IMPORTANT]
> Om du vill migrera från 3.x till den senaste versionen av tillägget avinstallerar du det gamla tillägget. Installera sedan version 4, som innehåller den uppdaterade konfigurationen för system tilldelad identitet och mottagare för att skicka mått till Azure Monitor mottagare.

När du installerar det nya tillägget aktiverar du automatiska delversionsuppgraderingar:
* På virtuella datorer med den klassiska distributionsmodellen anger du version om du installerar `4.*` tillägget via Azure Xplat CLI eller PowerShell.
* Inkludera Azure Resource Manager vm-distributionsmallen på den virtuella datorn `"autoUpgradeMinorVersion": true` i distributionsmodellen.

Du kan använda samma lagringskonto som du använde för LAD 3.x. 

## <a name="protected-settings"></a>Skyddade inställningar

Den här uppsättningen konfigurationsinformation innehåller känslig information som ska skyddas från offentlig vy. Den innehåller till exempel autentiseringsuppgifter för lagring. Inställningarna överförs till och lagras av tillägget i krypterad form.

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
storageAccountName | Namnet på lagringskontot där tillägget skriver data.
storageAccountEndPoint | (Valfritt) Slutpunkten som identifierar molnet där lagringskontot finns. Om den här inställningen saknas använder LAD som standard det offentliga Azure-molnet, `https://core.windows.net` . Om du vill använda ett lagringskonto i Azure Azure Government eller Azure China 21Vianet anger du det här värdet som obligatoriskt.
storageAccountSasToken | En [KONTO-SAS-token](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) för blob- och tabelltjänster ( `ss='bt'` ). Denna token gäller för containrar och objekt ( `srt='co'` ). Den ger behörighet att lägga till, skapa, lista, uppdatera och skriva ( `sp='acluw'` ). Ta *inte* med inledande frågetecken (?).
mdsdHttpProxy | (Valfritt) HTTP-proxyinformation tillägget måste ansluta till det angivna lagringskontot och slutpunkten.
sinksConfig | (Valfritt) Information om alternativa mål som mått och händelser kan levereras till. Följande avsnitt innehåller information om varje data mottagare som stöds av tillägget.

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

Det `sinksConfig` valfria avsnittet definierar fler mål som tillägget skickar insamlad information till. Matrisen `"sink"` innehåller ett -objekt för varje ytterligare data mottagare. Attributet `"type"` avgör de andra attributen i objektet.

Element | Värde
------- | -----
name | En sträng som används för att referera till den här mottagaren någon annanstans i tilläggskonfigurationen.
typ | Den typ av mottagare som definieras. Anger de andra värdena (om sådana finns) i instanser av den här typen.

Linux-diagnostiktillägget 4.0 stöder två typer av mottagare: `EventHub` och `JsonBlob` .

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

Posten `"sasURL"` innehåller den fullständiga URL:en, inklusive SAS-token, för den händelsehubb som data ska publiceras till. LAD kräver en SAS för att namnge en princip som gör det möjligt att skicka anspråket. Här är ett exempel:

* Skapa ett Event Hubs namnområde med namnet `contosohub` .
* Skapa en händelsehubb i namnområdet `syslogmsgs` .
* Skapa en princip för delad åtkomst på händelsehubben med `writer` namnet som gör att anspråket kan skickas.

Om du har skapat en SAS som är bra fram till midnatt UTC den 1 januari 2018 kan värdet se `sasURL` ut som i följande exempel.

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

Blobar lagras i en container som har samma namn som mottagaren. Reglerna Azure Storage för blobcontainernamn gäller för namnen på `JsonBlob` mottagare. Det innebär att namn måste ha mellan 3 och 63 gemener alfanumeriska ASCII-tecken eller bindestreck.

## <a name="public-settings"></a>Offentliga inställningar

Den offentliga inställningsstrukturen innehåller olika inställningsblock som styr den information som tillägget samlar in. Varje inställning, förutom `ladCfg` , är valfri. Om du anger mått eller syslog-samling `ladCfg` i måste du också ange `StorageAccount` . Du måste ange elementet `sinksConfig` för att aktivera Azure Monitor för mått från LAD 4.0.

```json
{
    "ladCfg":  { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "sinksConfig": { ... },
    "mdsdHttpProxy" : ""
}
```

Element | Värde
------- | -----
StorageAccount | Namnet på lagringskontot där tillägget skriver data. Måste vara det namn som anges i [de skyddade inställningarna](#protected-settings).
mdsdHttpProxy | (Valfritt) Proxyn som anges i [de skyddade inställningarna](#protected-settings). Om det privata värdet anges åsidosätts det offentliga värdet. Placera proxyinställningar som innehåller en hemlighet, till exempel ett lösenord, i [de skyddade inställningarna](#protected-settings).

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

Strukturen `ladCfg` styr insamlingen av mått och loggar för leverans till Azure Monitor Metrics-tjänsten och till andra data mottagare. Ange `performanceCounters` antingen `syslogEvents` eller eller båda. Ange även `metrics` strukturen.

Om du inte vill aktivera syslog eller måttsamling anger du en tom struktur för elementet, som `ladCfg` i det här exemplet: 

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {}
    }
```

Element | Värde
------- | -----
eventVolume | (Valfritt) Styr antalet partitioner som skapas i lagringstabellen. Värdet måste vara `"Large"` , `"Medium"` eller `"Small"` . Om värdet inte anges är standardvärdet `"Medium"` .
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
resourceId | Det Azure Resource Manager resurs-ID:t för den virtuella datorn eller vm-skalningsuppsättningen som den virtuella datorn tillhör. Ange även den här inställningen om konfigurationen använder någon `JsonBlob` mottagare.
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
Sjunker | (Valfritt) En kommaavgränsad lista med namn på mottagare som LAD skickar aggregerade måttresultat till. Alla aggregerade mått publiceras till varje mottagare i listan. Exempel: `"EHsink1, myjsonsink"`. Mer information finns i [`sinksConfig`](#sinksconfig). 
typ | Identifierar den faktiska providern av måttet.
klass | Tillsammans med `"counter"` identifierar det specifika måttet inom leverantörens namnområde.
räknare | Tillsammans med `"class"` identifierar det specifika måttet i providerns namnområde.
counterSpecifier | Identifierar det specifika måttet i Azure Monitor metrics-namnområdet.
Villkor | (Valfritt) Väljer en instans av objektet som måttet gäller för. Eller väljer aggregeringen för alla instanser av objektet. 
Samplerate | IS 8601-intervallet som anger med vilken hastighet råprover för det här måttet samlas in. Om värdet inte anges anges samlingsintervallet med värdet [`sampleRateInSeconds`](#ladcfg) . Den kortaste samplingsfrekvensen som stöds är 15 sekunder (PT15S).
unit | Definierar måttenheten. Bör vara någon av dessa strängar: `"Count"` , , , , , , `"Bytes"` `"Seconds"` `"Percent"` `"CountPerSecond"` `"BytesPerSecond"` `"Millisecond"` . Konsumenter av insamlade data förväntar sig att insamlade datavärden matchar den här enheten. LAD ignorerar det här fältet.
displayName | Etiketten som ska kopplas till data i Azure Monitor Mått. Den här etiketten är på det språk som anges av den associerade språkinställningen. LAD ignorerar det här fältet.

är `counterSpecifier` en godtycklig identifierare. Konsumenter av mått, till exempel Azure Portal och aviseringsfunktionen, använder som "nyckel" som identifierar ett mått eller en `counterSpecifier` instans av ett mått. 

För `builtin` mått rekommenderar vi värden som börjar med `counterSpecifier` `/builtin/` . Om du samlar in en specifik instans av ett mått kopplar du instansens identifierare till `counterSpecifier` värdet. Här är några exempel:

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
facilityName | Ett syslog-anläggningsnamn, till `"LOG\_USER"` exempel eller `"LOG\_LOCAL0"` . Mer information finns i avsnittet "anläggning" på [syslog-man-sidan.](http://man7.org/linux/man-pages/man3/syslog.3.html)
minSeverity | En allvarlighetsgrad för syslog, till exempel `"LOG\_ERR"` eller `"LOG\_INFO"` . Mer information finns i avsnittet "nivå" på [syslog man-sidan](http://man7.org/linux/man-pages/man3/syslog.3.html). Tillägget samlar in händelser som skickas till anläggningen på eller över den angivna nivån.

När du anger `syslogEvents` skriver LAD alltid data till en tabell i Azure Storage. Samma data kan skrivas till JSON-blobar eller Event Hubs eller båda. Men du kan inte inaktivera lagring av data i en tabell. 

Partitioneringsbeteendet för den här tabellen är detsamma som det som beskrivs för `performanceCounters` . Tabellnamnet är sammanfogningen av dessa strängar:

* `LinuxSyslog`
* Ett datum i formuläret "YYYYMMDD" som ändras var 10:e dag

Exempel är `LinuxSyslog20170410` och `LinuxSyslog20170609` .

### <a name="sinksconfig"></a>sinksConfig

Det `sinksConfig` valfria avsnittet gör det möjligt att skicka mått till Azure Monitor-mottagaren utöver lagringskontot och standardbladet gästmått.

> [!NOTE]
> Avsnittet `sinksConfig` kräver att system tilldelad identitet är aktiverat på de virtuella datorerna eller VM-skalningsuppsättningen. Du kan aktivera system tilldelad identitet via Azure Portal, CLI, PowerShell eller Azure Resource Manager. Följ de [detaljerade anvisningarna](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) eller se tidigare installationsexempel i den här artikeln. 

```json
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
```


### <a name="filelogs"></a>fileLogs

Avsnittet `fileLogs` styr insamlingen av loggfiler. LAD samlar in nya textrader när de skrivs till filen. Den skriver dem till tabellrader och/eller angivna mottagare, till exempel `JsonBlob` och `EventHub` .

> [!NOTE]
> `fileLogs`avbildas av en delkomponent av LAD som kallas `omsagent` . Om du `fileLogs` vill samla in ser du till att användaren har `omsagent` läsbehörighet för de filer som du anger. Den måste också ha körbehörighet för alla kataloger i sökvägen till filen. När LAD har installerats kan du kontrollera behörigheterna genom att köra `sudo su omsagent -c 'cat /path/to/file'` .

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
file | Den fullständiga sökvägen till loggfilen som ska övervakas och avbildas. Sökvägen är för en enskild fil. Den kan inte namnge en katalog eller innehålla jokertecken. Användarkontot `omsagent` måste ha läsbehörighet till filsökvägen.
tabell | (Valfritt) Den Azure Storage tabell där nya rader från "slutet" av filen skrivs. Tabellen måste finnas i det angivna lagringskontot, enligt vad som anges i den skyddade konfigurationen. 
Sjunker | (Valfritt) En kommaavgränsad lista med namn på fler mottagare som loggrader skickas till.

Antingen `"table"` eller eller båda måste `"sinks"` anges.

## <a name="metrics-supported-by-the-builtin-provider"></a>Mått som stöds av den inbyggda providern

> [!NOTE]
> Standardmåtten som LAD stöder sammanställs över alla filsystem, diskar eller namn. För icke-aggregerade mått kan du gå till det nyare Azure Monitor stöd för statistik för mottagare.

Måttleverantören `builtin` är en källa till mått som är mest intressanta för en bred uppsättning användare. Dessa mått är indela i fem breda klasser:

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
PercentInterruptTime | Procentandel tid som kör avbrott i maskinvara eller programvara och DPC (uppskjutna procedursamtal)
PercentUserTime | Av icke-inaktiv tid under aggregeringsfönstret, procentandelen tid som ägnats i användarläge med normal prioritet
PercentNiceTime | Av icke-inaktiv tid har procentandelen som spenderats på lägre (bra) prioritet
PercentPrivilegedTime | Av icke-inaktiv tid förbrukades procentandelen i privilegierat läge (kernel)

De första fyra räknarna ska summeras till 100 procent. De sista tre räknarna summeras också till 100 procent. Dessa tre räknare delar in summan av `PercentProcessorTime` , `PercentIOWaitTime` och `PercentInterruptTime` .

### <a name="builtin-metrics-for-the-memory-class"></a>inbyggda mått för klassen Memory

Måttklassen Minne innehåller information om minnesanvändning, växling och växling.

räknare | Innebörd
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
BytesTransmitted | Totalt antal byte som skickats sedan starten
ByteReceived | Totalt antal byte som tagits emot sedan starten
BytesTotal | Totalt antal byte som skickats eller tagits emot sedan starten
Paket som haröversatts | Totalt antal paket som skickats sedan starten
PacketsReceived | Totalt antal paket som tagits emot sedan starten
TotalRxErrors | Antal mottagningsfel sedan starten
TotalTxErrors | Antal överföringsfel sedan starten
TotalCollisions | Antal kollisioner som rapporterats av nätverksportarna sedan starten

### <a name="builtin-metrics-for-the-file-system-class"></a>inbyggda mått för filsystemklassen

Filsystemklassen med mått innehåller information om filsystemanvändning. Absoluta värden och procentvärden rapporteras som de skulle visas för en vanlig användare (inte rot).

Räknare | Innebörd
------- | -------
LedigtUtrymme | Tillgängligt diskutrymme i byte
UsedSpace | Använt diskutrymme i byte
PercentFreeSpace | Procentandel ledigt utrymme
PercentUsedSpace | Procentandel använt utrymme
PercentFreeInodes | Procentandel oanvända indexnoder (i noder)
PercentUsedInodes | Procentandel allokerade (används) i -odes summerade i alla filsystem
ByteReadPerSecond | Byte läses per sekund
BytesWrittenPerSecond | Byte skrivna per sekund
BytePerSecond | Byte som läses eller skrivs per sekund
ReadsPerSecond | Läsåtgärder per sekund
WritesPerSecond | Skrivåtgärder per sekund
TransfersPerSecond | Läs- eller skrivåtgärder per sekund

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
WriteBytesPerSecond | Antal byte som skrivits per sekund
BytesPerSecond | Antal lästa eller skrivna byte per sekund

## <a name="install-and-configure-lad-40"></a>Installera och konfigurera LAD 4.0

Du kan installera och konfigurera LAD 4.0 i Azure CLI eller i PowerShell.

### <a name="azure-cli"></a>Azure CLI

Om dina skyddade inställningar finns i filen *ProtectedSettings.jspå* och din offentliga konfigurationsinformation finns i *PublicSettings.jskör* du det här kommandot:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Kommandot förutsätter att du använder Azure Resource Management-läget i Azure CLI. Om du vill konfigurera LAD för den klassiska distributionsmodellens virtuella datorer växlar du till "asm"-läge ( ) och utelämnar `azure config mode asm` resursgruppens namn i kommandot . 

Mer information finns i [cli-dokumentationen för flera plattformar.](/cli/azure/authenticate-azure-cli)

### <a name="powershell"></a>PowerShell

Om dina skyddade inställningar finns i `$protectedSettings` variabeln och din offentliga konfigurationsinformation finns i `$publicSettings` variabeln kör du det här kommandot:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

## <a name="example-lad-40-configuration"></a>Exempel på LAD 4.0-konfiguration

Det här avsnittet, som baseras på föregående definitioner, innehåller ett exempel på konfiguration av LAD 4.0-tillägget och en förklaring. Om du vill använda det här exemplet i ditt fall använder du ditt eget lagringskontonamn, kontots SAS-token och Event Hubs SAS-token.

> [!NOTE]
> Beroende på om du använder Azure CLI eller PowerShell för att installera LAD, skiljer sig metoden för att tillhandahålla offentliga och skyddade inställningar: 
>
> * Om du använder Azure CLI sparar du följande inställningar för *attProtectedSettings.jspå* och *PublicSettings.jsvidare* för att använda föregående exempelkommando. 
> * Om du använder PowerShell sparar du följande inställningar i och `$protectedSettings` `$publicSettings` genom att köra `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Skyddade inställningar

De skyddade inställningarna konfigurerar:

* Ett lagringskonto.
* En matchande SAS-token för kontot.
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

* Ladda upp mätvärden för processortid i procent och mått för använt diskutrymme till `WADMetrics*` tabellen,
* Ladda upp meddelanden från `"user"` systemloggen och `"info"` allvarlighetsgraden till `LinuxSyslog*` tabellen.
* Ladda upp tilladdade rader i `/var/log/myladtestlog` filen till `MyLadTestLog` tabellen.

I varje fall överförs även data till:

* Azure Blob Storage. Containernamnet är enligt definitionen i `JsonBlob` mottagaren.
* En Event Hubs slutpunkt som anges i `EventHubs` mottagaren.

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
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
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

* Azure-plattformens måttdiagram och aviseringar känner till `resourceId` vilken virtuell dator du arbetar med. Den förväntar sig att hitta data för den virtuella datorn med `resourceId` hjälp av uppslagsnyckeln.
* Om du använder Autoskalning i Azure måste `resourceId` konfigurationen för autoskalning matcha `resourceId` den som används av LAD.
* `resourceId`är inbyggt i namnen på JSON-blobar som skrivits av LAD.

## <a name="view-your-data"></a>Visa dina data

Använd Azure Portal för att visa prestandadata eller ställa in aviseringar:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Skärmbild som visar Azure Portal. Måttet Använt diskutrymme har valts. Det resulterande diagrammet visas.":::

Data `performanceCounters` lagras alltid i en Azure Storage tabell. Azure Storage API:er är tillgängliga för många språk och plattformar.

Data som skickas `JsonBlob` till mottagare lagras i blobar i lagringskontot med namnet i [de skyddade inställningarna](#protected-settings). Du kan använda blobdata i alla Azure Blob Storage API:er.

Du kan också använda dessa gränssnittsverktyg för att komma åt data i Azure Storage:

* Visual Studio Server Explorer
* [Azure Lagringsutforskaren](https://azurestorageexplorer.codeplex.com/)

Följande skärmbild av en Azure Storage Explorer session visar de genererade Azure Storage och containrar från ett korrekt konfigurerat LAD 4.0-tillägg på en virtuell test-dator. Avbildningen matchar inte exakt [LAD 4.0-exempelkonfigurationen](#example-lad-40-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Skärmbild som visar Azure Storage Explorer.":::

Mer information om hur du använder meddelanden som publicerats till en Event Hubs-slutpunkt finns i relevant [Event Hubs dokumentation](../../event-hubs/event-hubs-about.md).

## <a name="next-steps"></a>Nästa steg

* I [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md)skapar du aviseringar för de mått som du samlar in.
* [Skapa övervakningsdiagram](../../azure-monitor/data-platform.md) för dina mått.
* [Skapa en VM-skalningsuppsättning](../linux/tutorial-create-vmss.md) med hjälp av dina mått för att styra autoskalning.