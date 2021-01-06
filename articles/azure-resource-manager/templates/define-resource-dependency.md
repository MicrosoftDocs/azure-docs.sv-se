---
title: Ange distributions ordning för resurser
description: Beskriver hur du anger en Azure-resurs som beroende av en annan resurs under distributionen. Beroenden säkerställer att resurser distribueras i rätt ordning.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: f6b63b066da06a17c3a2e51ab0f3ab9bf521a144
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934755"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definiera ordningen för att distribuera resurser i ARM-mallar

När du distribuerar resurser kan du behöva kontrol lera att vissa resurser finns före andra resurser. Du behöver till exempel en logisk SQL-Server innan du distribuerar en databas. Du upprättar den här relationen genom att markera en resurs som beroende av den andra resursen. Använd `dependsOn` elementet för att definiera ett explicit beroende. Använd **referens** -eller **list** funktionerna för att definiera ett implicit beroende.

Azure Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i deras beroende ordning. När resurserna inte är beroende av varandra distribuerar Resource Manager dem parallellt. Du behöver bara definiera beroenden för resurser som har distribuerats i samma mall.

## <a name="dependson"></a>dependsOn

I din Azure Resource Manager-mall (ARM-mall) kan `dependsOn` du med hjälp av elementet definiera en resurs som beroende av en eller flera resurser. Dess värde är en JavaScript Object Notation-matris (JSON) av strängar, som var och en är ett resurs namn eller ID. Matrisen kan innehålla resurser som är [villkorligt distribuerade](conditional-resource-deployment.md). När en villkorlig resurs inte distribueras tar Azure Resource Manager automatiskt bort den från de nödvändiga beroendena.

I följande exempel visas ett nätverks gränssnitt som är beroende av ett virtuellt nätverk, en nätverks säkerhets grupp och en offentlig IP-adress. Den fullständiga mallen finns i [snabb starts mal len för en virtuell Linux-dator](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json).

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

Även om du kan luta dig att använda `dependsOn` för att mappa relationer mellan dina resurser är det viktigt att du förstår varför du gör det. Om du till exempel vill dokumentera hur resurserna är anslutna, `dependsOn` är inte rätt metod. Du kan inte fråga vilka resurser som definierats i `dependsOn` elementet efter distributionen. Att ange onödiga beroenden saktar ned distributions tiden eftersom Resource Manager inte kan distribuera resurserna parallellt.

## <a name="child-resources"></a>Underordnade resurser

Ett implicit distributions beroende skapas inte automatiskt mellan en [underordnad resurs](child-resource-name-type.md) och den överordnade resursen. Om du behöver distribuera den underordnade resursen efter den överordnade resursen anger du `dependsOn` egenskapen.

I följande exempel visas en logisk SQL-Server och-databas. Observera att ett explicit beroende har definierats mellan databasen och servern, även om databasen är underordnad servern.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

Den fullständiga mallen finns i [snabb starts mall för Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json).

## <a name="reference-and-list-functions"></a>referens-och list funktioner

[Funktionen Reference](template-functions-resource.md#reference) gör det möjligt för ett uttryck att härleda sitt värde från andra JSON-namn och värdepar eller körnings resurser. [List * Functions](template-functions-resource.md#list) returnerar värden för en resurs från en List åtgärd.

Referens-och list uttryck deklarerar implicit om att en resurs är beroende av en annan resurs. När det är möjligt bör du använda en implicit referens för att undvika att lägga till ett onödigt beroende.

Om du vill framtvinga ett implicit beroende, referera till resursen efter namn, inte resurs-ID. Om du skickar resurs-ID: t till referens-eller List funktionerna skapas inte en implicit referens.

Det allmänna formatet för `reference` funktionen är:

```json
reference('resourceName').propertyPath
```

Det allmänna formatet för `listKeys` funktionen är:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

I följande exempel beror en CDN-slutpunkt uttryckligen på CDN-profilen och är implicit beroende av en webbapp.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

Mer information finns i [referens funktion](template-functions-resource.md#reference).

## <a name="depend-on-resources-in-a-loop"></a>Är beroende av resurser i en slinga

Du kan använda två alternativ för att distribuera resurser som är beroende av resurser i en [kopierings slinga](copy-resources.md). Du kan antingen ange ett beroende för enskilda resurser i slingan eller på hela slingan.

> [!NOTE]
> I de flesta fall bör du ange beroendet för enskilda resurser i kopierings slingan. Är bara beroende av hela loopen när du behöver alla resurser i slingan för att kunna existera innan du skapar nästa resurs. Om du anger beroendet för hela loopen blir beroende diagrammet expanderat avsevärt, särskilt om de upprepande resurserna är beroende av andra resurser. De expanderade beroendena gör det svårt för distributionen att slutföras effektivt.

I följande exempel visas hur du distribuerar flera virtuella datorer. Mallen skapar samma antal nätverks gränssnitt. Varje virtuell dator är beroende av ett nätverks gränssnitt, i stället för hela loopen.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

I följande exempel visas hur du distribuerar tre lagrings konton innan du distribuerar den virtuella datorn. Observera att `copy` elementet har `name` angetts till `storagecopy` och att `dependsOn` elementet för den virtuella datorn också är inställt på `storagecopy` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>Cirkulära beroenden

Resource Manager identifierar cirkulära beroenden vid verifiering av mall. Om du får ett fel meddelande om ett cirkulärt beroende ska du utvärdera din mall för att se om det finns några beroenden som kan tas bort. Om det inte fungerar att ta bort beroenden kan du undvika cirkulära beroenden genom att flytta några distributions åtgärder till underordnade resurser. Distribuera de underordnade resurserna efter de resurser som har cirkulärt beroende. Anta till exempel att du distribuerar två virtuella datorer, men du måste ange egenskaper för var och en som refererar till den andra. Du kan distribuera dem i följande ordning:

1. vm1
2. VM2
3. Tillägget på VM1 är beroende av VM1 och VM2. Tillägget anger värden för VM1 som hämtas från VM2.
4. Tillägget på VM2 är beroende av VM1 och VM2. Tillägget anger värden för VM2 som hämtas från VM1.

Information om hur du bedömer distributions ordningen och löser beroende fel finns i [Felsöka vanliga problem med Azure-distributioner med Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom självstudierna finns i [Självstudier: skapa arm-mallar med beroende resurser](template-tutorial-create-templates-with-dependent-resources.md).
* En Microsoft Learn-modul som täcker resurs beroenden finns i [hantera komplexa moln distributioner med hjälp av avancerade funktioner för ARM-mallar](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Rekommendationer för hur du ställer in beroenden finns i [metod tips för ARM-mallar](template-best-practices.md).
* Information om hur du felsöker beroenden under distributionen finns i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](common-deployment-errors.md).
* Information om hur du skapar Azure Resource Manager-mallar finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* En lista över tillgängliga funktioner i en mall finns i funktioner i [arm-mallar](template-functions.md).
