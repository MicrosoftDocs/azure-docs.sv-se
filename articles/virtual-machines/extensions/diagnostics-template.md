---
title: Lägg till övervakning & diagnostik till en virtuell Azure-dator
description: Använd en Azure Resource Manager mall för att skapa en ny virtuell Windows-dator med Azure Diagnostics-tillägget.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 05/31/2017
ms.openlocfilehash: 6d365c7e927c11f52b97fbb0cc01a7aa37ad5afd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560063"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Använd övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager mallar
Azure-diagnostik-tillägget innehåller funktioner för övervakning och diagnostik på en Windows-baserad virtuell Azure-dator. Du kan aktivera dessa funktioner på den virtuella datorn genom att inkludera tillägget som en del av Azure Resource Manager-mallen. Mer information om hur du inkluderar tillägg som en del av en mall för virtuella datorer finns i [redigera Azure Resource Manager mallar med VM-tillägg](../windows/template-description.md#extensions) . I den här artikeln beskrivs hur du kan lägga till Azure-diagnostik-tillägget i en mall för virtuella Windows-datorer.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Lägg till Azure-diagnostik-tillägget till resurs definitionen för virtuell dator
Om du vill aktivera tillägget för diagnostik på en virtuell Windows-dator måste du lägga till tillägget som en VM-resurs i Resource Manager-mallen.

För en enkel Resource Manager-baserad virtuell dator lägger du till tilläggs konfigurationen till *resurs* -matrisen för den virtuella datorn: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

En annan vanlig konvention är att lägga till tilläggs konfigurationen i noden rot resurser i mallen i stället för att definiera den under noden resurser för den virtuella datorn. Med den här metoden måste du uttryckligen ange en hierarkisk relation mellan tillägget och den virtuella datorn med värdena *namn* och *typ* . Exempel: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Tillägget är alltid associerat med den virtuella datorn. du kan antingen definiera det direkt under nodens datornod direkt eller definiera det på bas nivå och använda den hierarkiska namngivnings konventionen för att koppla den till den virtuella datorn.

För Virtual Machine Scale Sets anges tilläggets konfiguration i egenskapen *extensionProfile* för *VirtualMachineProfile*.

*Utgivar* egenskapen med värdet **Microsoft. Azure. Diagnostics** och *Type* -egenskapen med värdet **IaaSDiagnostics** identifierar Azure-diagnostik-tillägget unikt.

Värdet för egenskapen *Name* kan användas för att referera till tillägget i resurs gruppen. Om du ställer in det på **Microsoft. Insights. VMDiagnosticsSettings** kan det enkelt identifieras av Azure Portal se till att övervaknings diagrammen visas korrekt i Azure Portal.

*TypeHandlerVersion* anger vilken version av tillägget som du vill använda. Genom att ange den lägre versionen av *aktiverat autoupgrademinorversion* till **True** ser du till att du får den senaste lägre versionen av tillägget som är tillgänglig. Vi rekommenderar starkt att du alltid ställer in *aktiverat autoupgrademinorversion* så att du alltid är **sann** så att du alltid kommer att använda det senaste tillgängliga diagnostikprogrammet med alla nya funktioner och fel korrigeringar. 

Elementet *Settings* innehåller konfigurations egenskaper för det tillägg som kan ställas in och läsas tillbaka från tillägget (kallas ibland för offentlig konfiguration). Egenskapen *xmlcfg* innehåller XML-baserad konfiguration för diagnostikloggar, prestanda räknare som samlas in av Diagnostics-agenten. Se [konfigurations schema för diagnostik](../../azure-monitor/agents/diagnostics-extension-schema-windows.md) om du vill ha mer information om själva XML-schemat. En vanlig metod är att lagra den faktiska XML-konfigurationen som en variabel i Azure Resource Manager-mallen och sedan sammanfoga och base64 koda dem för att ange värdet för *xmlcfg*. Information om hur du lagrar XML i variabler finns i avsnittet om [konfigurations variabler för diagnostik](#diagnostics-configuration-variables) . Egenskapen *storageAccount* anger namnet på det lagrings konto som diagnostikdata överförs till. 

Egenskaperna i *protectedSettings* (kallas ibland privat konfiguration) kan anges men kan inte läsas tillbaka när de har angetts. Den skrivskyddade typen av *protectedSettings* gör det användbart för att lagra hemligheter som lagrings konto nyckel där diagnostikdata skrivs.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Ange ett lagrings konto för diagnostik som parametrar
JSON-kodfragmentet för Diagnostics Extension ovan förutsätter två parametrar *existingdiagnosticsStorageAccountName* och *existingdiagnosticsStorageResourceGroup* för att ange det lagrings konto för diagnostik där diagnostikdata lagras. Genom att ange ett lagrings konto för diagnostik som en parameter är det enkelt att ändra diagnostikens lagrings konto i olika miljöer, till exempel kanske du vill använda ett annat konto för diagnostik-lagring för testning och en annan för produktions distributionen.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Det är bäst att ange ett lagrings konto för diagnostik i en annan resurs grupp än resurs gruppen för den virtuella datorn. En resurs grupp kan anses vara en distributions enhet med en egen livs längd, en virtuell dator kan distribueras och omdistribueras eftersom nya konfigurationer av konfigurationer görs till den, men du kanske vill fortsätta att lagra diagnostikdata i samma lagrings konto över dessa distributioner av virtuella datorer. Om du har lagrings kontot i en annan resurs kan lagrings kontot ta emot data från olika distributioner av virtuella datorer, vilket gör det enkelt att felsöka problem i olika versioner.

> [!NOTE]
> Om du skapar en mall för virtuella Windows-datorer från Visual Studio kan standard lagrings kontot vara inställt på att använda samma lagrings konto där den virtuella hård disken har laddats upp. Detta är för att förenkla den första installationen av den virtuella datorn. Omvärdera mallen till att använda ett annat lagrings konto som kan skickas som en parameter. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Variabler för konfiguration av diagnostik
Föregående JSON-kodfragment för diagnostik definierar en *accountid* -variabel för att förenkla hämtningen av lagrings konto nyckeln för diagnostik-lagringen:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

*Xmlcfg* -egenskapen för Diagnostics-tillägget definieras med flera variabler som sammanfogas tillsammans. Värdena för dessa variabler är i XML så att de måste undantas korrekt när du anger JSON-variablerna.

I följande exempel beskrivs XML för diagnostik-konfiguration som samlar in prestanda räknare på standard system nivå tillsammans med vissa Windows-händelseloggar och infrastruktur loggar för diagnostik. Den har avvisats och formaterats korrekt, så att konfigurationen kan klistras in direkt i avsnittet variabler i mallen. I [konfigurations schema för diagnostik](../../azure-monitor/agents/diagnostics-extension-schema-windows.md) finns ett mer lättläst exempel på konfigurations-XML.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

XML-noden för mått definition i ovanstående konfiguration är ett viktigt konfigurations element som definierar hur de prestanda räknare som definierats tidigare i XML-filen i *PerformanceCounter* -noden aggregeras och lagras. 

> [!IMPORTANT]
> Dessa mått styr övervaknings diagrammen och aviseringarna i Azure Portal.  Noden **mått** med *resourceID* och **MetricAggregation** måste ingå i diagnostik-konfigurationen för din virtuella dator om du vill se data för VM-övervakning i Azure Portal. 
> 
> 

I följande exempel visas XML för mått definitioner: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Attributet *resourceID* identifierar unikt den virtuella datorn i din prenumeration. Se till att använda funktionerna Subscription () och resourceGroup () så att mallen automatiskt uppdaterar dessa värden baserat på den prenumeration och resurs grupp som du distribuerar till.

Om du skapar flera Virtual Machines i en slinga måste du fylla i värdet *resourceID* med en copyIndex ()-funktion för att kunna särskilja varje enskild virtuell dator. *XmlCfg* -värdet kan uppdateras för att stödja detta på följande sätt:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

MetricAggregation-värdet för *PT1M* och *PT1H* indikerar en agg regering över en minut och en agg regering över en timme.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics-tabeller i lagring
Måtts konfigurationen ovan genererar tabeller i ditt diagnostik lagrings konto med följande namn konventioner:

* **WADMetrics**: standardprefix för alla WADMetrics-tabeller
* **PT1H** eller **PT1M**: indikerar att tabellen innehåller sammanställda data över 1 timme eller 1 minut
* **P10D**: visar att tabellen innehåller data i 10 dagar från när tabellen började samla in data
* **V2S**: strängkonstant
* **ÅÅÅÅMMDD**: datumet då tabellen började samla in data

Exempel: *WADMetricsPT1HP10DV2S20151108* innehåller mått data som sammanställts under en timme för 10 dagar med början 11-Nov-2015    

Varje WADMetrics-tabell innehåller följande kolumner:

* **PartitionKey**: partitionsnyckel konstrueras baserat på värdet *resourceID* för att unikt identifiera VM-resursen. Exempelvis: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: följer formatet `<Descending time tick>:<Performance Counter Name>` . Tids Skale beräkningen för fallande timmar är Max tids skal minus tiden i början av agg regerings perioden. Exempel: om exempel perioden startades 10-nov-2015 och 00:00Hrs UTC skulle beräkningen vara: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)` . För prestanda räknaren tillgängliga minnes byte kommer rad nyckeln att se ut så här: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: är namnet på prestanda räknaren. Detta matchar den *counterSpecifier* som definierats i XML-konfigurationen.
* **Maximum**: det maximala värdet för prestanda räknaren över samlings perioden.
* **Minimum**: det minimala värdet för prestanda räknaren över samlings perioden.
* **Totalt**: summan av alla värden i prestanda räknaren som rapporter ATS under samlings perioden.
* **Antal**: det totala antalet värden som rapporter ATS för prestanda räknaren.
* **Genomsnitt**: det genomsnittliga värdet (total/Count) för prestanda räknaren över samlings perioden.

## <a name="next-steps"></a>Nästa steg
* En fullständig exempel mall för en virtuell Windows-dator med diagnostik-tillägg finns i [201-VM-Monitoring-Diagnostics-Extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Distribuera Azure Resource Manager-mallen med [Azure PowerShell](../windows/ps-template.md) eller [Azure kommando rad](../linux/create-ssh-secured-vm-from-template.md)
* Läs mer om hur du [redigerar Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md)