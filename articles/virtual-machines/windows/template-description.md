---
title: Virtuella datorer i en Azure Resource Manager mall | Microsoft Azure
description: Lär dig mer om hur den virtuella dator resursen definieras i en Azure Resource Manager mall.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: 32b73be3faf6eedb92220725b292a3e69cf7f965
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556000"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuella datorer i en Azure Resource Manager-mall

Den här artikeln beskriver aspekter av en Azure Resource Manager-mall som gäller för virtuella datorer. Den här artikeln beskriver inte en fullständig mall för att skapa en virtuell dator. för att du behöver resurs definitioner för lagrings konton, nätverks gränssnitt, offentliga IP-adresser och virtuella nätverk. Mer information om hur dessa resurser kan definieras tillsammans finns i [genom gången av Resource Manager-mallen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

Det finns många [mallar i galleriet](https://azure.microsoft.com/documentation/templates/?term=VM) som innehåller den virtuella dator resursen. Alla element som kan inkluderas i en mall beskrivs inte här.

 

I det här exemplet visas ett typiskt resurs avsnitt i en mall för att skapa ett angivet antal virtuella datorer:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Det här exemplet använder ett lagrings konto som skapats tidigare. Du kan skapa lagrings kontot genom att distribuera det från mallen. Exemplet förlitar sig även på ett nätverks gränssnitt och dess beroende resurser som definieras i mallen. Dessa resurser visas inte i exemplet.
>
>

## <a name="api-version"></a>API-version

När du distribuerar resurser med en mall måste du ange en version av API: et som ska användas. Exemplet visar den virtuella dator resursen med det här API version-elementet:

```json
"apiVersion": "2016-04-30-preview",
```

Den version av API: n som du anger i mallen påverkar vilka egenskaper du kan definiera i mallen. I allmänhet bör du välja den senaste API-versionen när du skapar mallar. För befintliga mallar kan du välja om du vill fortsätta använda en tidigare API-version eller uppdatera din mall för den senaste versionen för att dra nytta av nya funktioner.

Använd de här möjligheterna för att hämta de senaste API-versionerna:

- REST API – [Visa alla resurs leverantörer](/rest/api/resources/providers)
- PowerShell- [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI – [AZ Provider show](/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parametrar och variabler

[Parametrar](../../azure-resource-manager/templates/template-syntax.md) gör det enkelt för dig att ange värden för mallen när du kör den. Det här parameter avsnittet används i exemplet:

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

När du distribuerar exempel mal len anger du värden för namn och lösen ord för administratörs kontot på varje virtuell dator och antalet virtuella datorer som ska skapas. Du kan välja att ange parameter värden i en separat fil som hanteras med mallen eller ange värden när du uppmanas att göra det.

[Variabler](../../azure-resource-manager/templates/template-syntax.md) gör det enkelt för dig att konfigurera värden i mallen som används upprepade gånger i den eller som kan ändras över tid. Avsnittet med följande variabler används i exemplet:

```json
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

När du distribuerar exempel mal len används variabel värden för namnet och identifieraren för det tidigare skapade lagrings kontot. Variabler används också för att ange inställningar för diagnostiskt tillägg. Använd de [bästa metoderna för att skapa Azure Resource Manager mallar](../../azure-resource-manager/templates/template-best-practices.md) som hjälper dig att bestämma hur du vill strukturera parametrarna och variablerna i mallen.

## <a name="resource-loops"></a>Resurs slingor

När du behöver fler än en virtuell dator för ditt program kan du använda ett kopierings element i en mall. Detta valfria element upprepas genom att skapa antalet virtuella datorer som du har angett som parameter:

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Lägg också märke till i exemplet att loop-indexet används när du anger några av resursens värden. Om du till exempel har angett ett instans antal på tre, är namnen på operativ system diskarna myOSDisk1, myOSDisk2 och myOSDisk3:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>I det här exemplet används hanterade diskar för de virtuella datorerna.
>
>

Tänk på att om du skapar en loop för en resurs i mallen kan du behöva använda loopen när du skapar eller använder andra resurser. Till exempel kan flera virtuella datorer inte använda samma nätverks gränssnitt, så om din mall loopar genom att skapa tre virtuella datorer måste den också gå igenom skapandet av tre nätverks gränssnitt. När du tilldelar ett nätverks gränssnitt till en virtuell dator används loop-indexet för att identifiera det:

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Beroenden

De flesta resurser är beroende av att andra resurser fungerar korrekt. Virtuella datorer måste associeras med ett virtuellt nätverk och för att göra det krävs ett nätverks gränssnitt. [DependsOn](../../azure-resource-manager/templates/define-resource-dependency.md) -elementet används för att kontrol lera att nätverks gränssnittet är klart att användas innan de virtuella datorerna skapas:

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resource Manager distribuerar parallellt med alla resurser som inte är beroende av en annan resurs som distribueras. Var försiktig när du anger beroenden eftersom du oavsiktligt kan minska distributionen genom att ange onödiga beroenden. Beroenden kan kopplas samman mellan flera resurser. Nätverks gränssnittet är till exempel beroende av den offentliga IP-adressen och de virtuella nätverks resurserna.

Hur vet du om ett beroende krävs? Titta på de värden som du angav i mallen. Om ett element i den virtuella datorns resurs definition pekar på en annan resurs som har distribuerats i samma mall, behöver du ett beroende. Ditt exempel på en virtuell dator definierar till exempel en nätverks profil:

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Nätverks gränssnittet måste finnas om du vill ange den här egenskapen. Därför behöver du ett beroende. Du måste också ange ett beroende när en resurs (underordnad) definieras inom en annan resurs (en överordnad). Diagnostikinställningar och anpassade skript tillägg är till exempel båda definierade som underordnade resurser för den virtuella datorn. De kan inte skapas förrän den virtuella datorn finns. Därför markeras båda resurserna som beroende av den virtuella datorn.

## <a name="profiles"></a>Profiler

Flera profil element används när du definierar en resurs för virtuella datorer. Vissa är obligatoriska och vissa är valfria. Till exempel krävs hardwareProfile-, osProfile-, storageProfile-och networkProfile-element, men diagnosticsProfile är valfritt. Dessa profiler definierar inställningar som:
   
- [ändra](../sizes.md)
- [namn](/azure/architecture/best-practices/resource-naming) och autentiseringsuppgifter
- disk-och [operativ system inställningar](cli-ps-findimage.md)
- [nätverks gränssnitt](/previous-versions/azure/virtual-network/virtual-network-deploy-multinic-classic-ps) 
- startdiagnostik

## <a name="disks-and-images"></a>Diskar och avbildningar
   
I Azure kan VHD-filer representera [diskar eller avbildningar](../managed-disks-overview.md). När operativ systemet i en VHD-fil är specialiserat för att vara en specifik virtuell dator kallas det för en disk. När operativ systemet i en VHD-fil generaliseras för att användas för att skapa många virtuella datorer kallas det för en avbildning.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Skapa nya virtuella datorer och nya diskar från en plattforms avbildning

När du skapar en virtuell dator måste du bestämma vilket operativ system som ska användas. ImageReference-elementet används för att definiera operativ systemet för en ny virtuell dator. Exemplet visar en definition för ett Windows Server-operativ system:

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Om du vill skapa ett Linux-operativsystem kan du använda den här definitionen:

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Konfigurations inställningarna för operativ system disken tilldelas till osDisk-elementet. Exemplet definierar en ny hanterad disk med cachelagring-läget inställt på **readwrite** och att disken skapas från en [plattforms avbildning](cli-ps-findimage.md):

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Skapa nya virtuella datorer från befintliga hanterade diskar

Om du vill skapa virtuella datorer från befintliga diskar tar du bort imageReference-och osProfile-elementen och definierar dessa disk inställningar:

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Skapa nya virtuella datorer från en hanterad avbildning

Om du vill skapa en virtuell dator från en hanterad avbildning ändrar du imageReference-elementet och definierar dessa disk inställningar:

```json
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Anslut data diskar

Du kan också lägga till data diskar i de virtuella datorerna. [Antalet diskar](../sizes.md) beror på storleken på den operativ system disk som du använder. När de virtuella datorerna har angetts till Standard_DS1_v2 är det maximala antalet data diskar som kan läggas till i dem två. I exemplet läggs en hanterad data disk till i varje virtuell dator:

```json
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Tillägg

Även om [tillägg](../extensions/features-windows.md) är en separat resurs är de nära kopplade till virtuella datorer. Tillägg kan läggas till som en underordnad resurs för den virtuella datorn eller som en separat resurs. Exemplet visar det [diagnostiska tillägg](../extensions/diagnostics-template.md) som läggs till i de virtuella datorerna:

```json
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Den här tilläggs resursen använder variabeln storageName och de diagnostiska variablerna för att ange värden. Om du vill ändra de data som samlas in av det här tillägget kan du lägga till fler prestanda räknare i wadperfcounters-variabeln. Du kan också välja att spara diagnostikdata i ett annat lagrings konto än där de virtuella dator diskarna lagras.

Det finns många tillägg som du kan installera på en virtuell dator, men den mest användbara är förmodligen det [anpassade skript tillägget](../extensions/custom-script-windows.md). I exemplet körs ett PowerShell-skript med namnet start.ps1 på varje virtuell dator när det startas första gången:

```json
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

start.ps1-skriptet kan utföra många konfigurations åtgärder. De data diskar som läggs till i de virtuella datorerna i exemplet initieras till exempel inte. Du kan använda ett anpassat skript för att initiera dem. Om du har flera start uppgifter kan du använda start.ps1-filen för att anropa andra PowerShell-skript i Azure Storage. Exemplet använder PowerShell, men du kan använda valfri skript metod som är tillgänglig på det operativ system som du använder.

Du kan se status för installerade tillägg från inställningarna för tillägg i portalen:

![Hämta tilläggs status](./media/template-description/virtual-machines-show-extensions.png)

Du kan också hämta tilläggs information med hjälp av PowerShell **-kommandot Get-AzVMExtension** , det **virtuella dator tillägget Hämta** Azure CLI-kommandot eller **Hämta tilläggs information** REST API.

## <a name="deployments"></a>Distributioner

När du distribuerar en mall spårar Azure de resurser som du har distribuerat som en grupp och tilldelar automatiskt ett namn till den här distribuerade gruppen. Namnet på distributionen är detsamma som namnet på mallen.

Om du är nyfiken på statusen för resurser i distributionen kan du Visa resurs gruppen i Azure Portal:

![Hämta distributions information](./media/template-description/virtual-machines-deployment-info.png)
    
Det går inte att använda samma mall för att skapa resurser eller uppdatera befintliga resurser. När du använder kommandon för att distribuera mallar har du möjlighet att säga vilket [läge](../../azure-resource-manager/templates/deploy-powershell.md) du vill använda. Läget kan anges till antingen **fullständig** eller **stegvis**. Standardvärdet är att göra stegvisa uppdateringar. Var försiktig när du använder det **fullständiga** läget eftersom du kan ta bort resurser av misstag. När du anger att läget ska **slutföras**, tar Resource Manager bort alla resurser i resurs gruppen som inte finns i mallen.

## <a name="next-steps"></a>Nästa steg

- Skapa en egen mall med hjälp av [redigera Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md).
- Distribuera mallen som du skapade med [skapa en virtuell Windows-dator med en Resource Manager-mall](ps-template.md).
- Lär dig hur du hanterar de virtuella datorer som du skapade genom att granska [skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen](tutorial-manage-vm.md).
- För JSON-syntax och egenskaper för resurs typer i mallar, se [Azure Resource Manager Template Reference](/azure/templates/).