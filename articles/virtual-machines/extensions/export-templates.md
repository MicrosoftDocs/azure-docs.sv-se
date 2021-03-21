---
title: Exportera Azure-resurs grupper som innehåller VM-tillägg
description: Exportera Resource Manager-mallar som innehåller tillägg för virtuella datorer.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 12/05/2016
ms.openlocfilehash: df1ae43b2c6a74448a6782a43fb86f8f4939b13a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560012"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportera resurs grupper som innehåller VM-tillägg

Azures resurs grupper kan exporteras till en ny Resource Manager-mall som sedan kan distribueras om. Export processen tolkar befintliga resurser och skapar en Resource Manager-mall som när de distribuerade resultaten i en liknande resurs grupp. När du använder export alternativet resurs grupp mot en resurs grupp som innehåller tillägg för virtuella datorer, måste flera objekt beaktas, till exempel tilläggets kompatibilitet och skyddade inställningar.

Det här dokumentet beskriver hur export processen för resurs grupper fungerar för tillägg till virtuella datorer, inklusive en lista över tillägg som stöds och information om hur du hanterar skyddade data.

## <a name="supported-virtual-machine-extensions"></a>Tillägg för virtuella datorer som stöds

Många tillägg för virtuella datorer är tillgängliga. Det går inte att exportera alla tillägg till en Resource Manager-mall med hjälp av funktionen "Automation-skript". Om ett tillägg för virtuell dator inte stöds måste det placeras manuellt i den exporterade mallen.

Följande tillägg kan exporteras med Automation-skript funktionen.

> Acronis säkerhets kopiering, Acronis Backup Linux, BG info, BMC CTM agent Linux, BMC CTM agent Windows, chefs klient, anpassat skript, anpassat skript tillägg, anpassat skript för Linux, Datadog Linux-Agent, Datadog Windows-agent, Docker-tillägg, DSC-tillägg, dynaTrace Linux, dynaTrace Windows, HPE Security Application Defender, IaaS antimalware, IaaS-diagnostik, Linux kock-klient, Linux-diagnostik, OS-uppdatering för Linux, Puppet-agent, plats dygnet , Platsens 24: a Linux-server, plats dygnet runt Windows Server, Trend Micro DSA, Trend Micro DSA Linux, VM-åtkomst för Linux, VM-åtkomst för Linux, VM-ögonblicksbild, VM-ögonblicksbild Linux

## <a name="export-the-resource-group"></a>Exportera resurs gruppen

Utför följande steg för att exportera en resurs grupp till en återanvändbar mall:

1. Logga in på Azure Portal
2. Klicka på resurs grupper på menyn hubb
3. Välj mål resurs grupp i listan
4. I bladet resurs grupp klickar du på Automation-skript

![Exportera mall](./media/export-templates/template-export.png)

Skriptet Azure Resource Manager automations skapar en Resource Manager-mall, en parameter fil och flera exempel på distributions skript som PowerShell och Azure CLI. Den exporterade mallen kan nu hämtas med hjälp av knappen Ladda ned, som läggs till som en ny mall i biblioteket eller omdistribueras med hjälp av knappen distribuera.

## <a name="configure-protected-settings"></a>Konfigurera skyddade inställningar

Många Azure Virtual Machine-tillägg innehåller en konfiguration för skyddade inställningar som krypterar känsliga data, till exempel autentiseringsuppgifter och konfigurations strängar. Skyddade inställningar exporteras inte med Automation-skriptet. Om det behövs måste de skyddade inställningarna läggas till i den exporterade mallen igen.

### <a name="step-1---remove-template-parameter"></a>Steg 1 – ta bort mallparameter

När resurs gruppen har exporter ATS skapas en enskild mallparameter för att tillhandahålla ett värde för de exporterade skyddade inställningarna. Den här parametern kan tas bort. Ta bort parametern genom att titta igenom parameter listan och ta bort den parameter som liknar det här JSON-exemplet.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Steg 2 – Hämta egenskaper för skyddat alternativ

Eftersom varje skyddad inställning har en uppsättning obligatoriska egenskaper måste en lista över dessa egenskaper samlas in. Varje parameter i konfigurationen för skyddade inställningar finns i [Azure Resource Manager schema på GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Det här schemat innehåller bara parameter uppsättningar för de tillägg som anges i översikts avsnittet i det här dokumentet. 

I lagrings platsen för schemat söker du efter det önskade tillägget, i det här exemplet `IaaSDiagnostics` . När objektet Extensions `protectedSettings` har hittats noterar du varje parameter. I exemplet på `IaasDiagnostic` tillägget är Kräv parametrar `storageAccountName` , `storageAccountKey` och `storageAccountEndPoint` .

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Steg 3 – återskapa den skyddade konfigurationen

I den exporterade mallen söker `protectedSettings` du efter och ersätter objektet exporterat skyddat objekt med en ny som innehåller nödvändiga tilläggs parametrar och ett värde för var och en.

I exemplet på `IaasDiagnostic` tillägget skulle den nya skyddade inställnings konfigurationen se ut som i följande exempel:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Den sista tilläggs resursen ser ut ungefär som följande JSON-exempel:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
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
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Om du använder mallparametrar för att ange egenskaps värden måste du skapa dem. När du skapar mallparametrar för skyddade inställnings värden ska du se till att använda `SecureString` parameter typen så att känsliga värden skyddas. Mer information om hur du använder parametrar finns i [redigera Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md).

I exemplet för `IaasDiagnostic` tillägget skapas följande parametrar i avsnittet parametrar i Resource Manager-mallen.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

I det här läget kan mallen distribueras med hjälp av en metod för distribution av mallar.
