---
title: Ange distributions ordning för resurser
description: Beskriver hur du anger en resurs som beroende av en annan resurs under distributionen för att säkerställa att resurser distribueras i rätt ordning.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 933764f1930bd6c9e21d4ccffbde1bb93bbc9613
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672822"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definiera ordningen för att distribuera resurser i ARM-mallar

När du distribuerar en resurs kan du behöva kontrol lera att det finns andra resurser innan den distribueras. Du behöver till exempel en logisk SQL-Server innan du distribuerar en databas. Du definierar den här relationen genom att markera en resurs som beroende av den andra resursen. Du definierar ett beroende med **dependsOn** -elementet eller med hjälp av **referens** funktionen.

Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i beroendeordning. När resurserna inte är beroende av varandra distribuerar Resource Manager dem parallellt. Du behöver bara definiera beroenden för resurser som har distribuerats i samma mall.

## <a name="dependson"></a>dependsOn

I din mall kan du med dependsOn-elementet definiera en resurs som beroende av en eller flera resurser. Värdet är en JSON-matris med strängar som var och en är ett resurs namn. Matrisen kan innehålla resurser som är [villkorligt distribuerade](conditional-resource-deployment.md). När en villkorlig resurs inte distribueras tar Azure Resource Manager automatiskt bort den från de nödvändiga beroendena.

I följande exempel visas en skalnings uppsättning för virtuella datorer som är beroende av en belastningsutjämnare, ett virtuellt nätverk och en slinga som skapar flera lagrings konton. De här andra resurserna visas inte i följande exempel, men de måste finnas någon annan stans i mallen.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "apiVersion": "2016-03-30",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

I föregående exempel ingår ett beroende på de resurser som skapas via en Copy-loop med namnet **storageLoop**. Ett exempel finns i [skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).

När du definierar beroenden kan du inkludera resurs leverantörens namn område och resurs typ för att undvika tvetydighet. Om du till exempel vill klargöra en belastningsutjämnare och ett virtuellt nätverk som kan ha samma namn som andra resurser, använder du följande format:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

Även om du kan luta dig att använda dependsOn för att mappa relationer mellan dina resurser är det viktigt att du förstår varför du gör det. Om du till exempel vill dokumentera hur resurser är anslutna till varandra är dependsOn inte rätt metod. Du kan inte fråga vilka resurser som definierats i dependsOn-elementet efter distributionen. Genom att använda dependsOn kan du påverka distributions tiden eftersom Resource Manager inte distribuerar parallellt två resurser som har ett beroende.

## <a name="child-resources"></a>Underordnade resurser

Med egenskapen resurser kan du ange underordnade resurser som är relaterade till den resurs som definieras. Underordnade resurser kan bara definieras fem nivåer djup. Det är viktigt att Observera att ett implicit distributions beroende inte skapas mellan en underordnad resurs och den överordnade resursen. Om du vill att den underordnade resursen ska distribueras efter den överordnade resursen måste du uttryckligen ange det beroende med egenskapen dependsOn.

Varje överordnad resurs accepterar bara vissa resurs typer som underordnade resurser. Godkända resurs typer anges i det [mall schema](https://github.com/Azure/azure-resource-manager-schemas) som tillhör den överordnade resursen. Namnet på den underordnade resurs typen innehåller namnet på den överordnade resurs typen, t. ex. **Microsoft. Web/Sites/config** och **Microsoft. Web/Sites/Extensions** är både underordnade resurser för **Microsoft. Web/Sites**.

I följande exempel visas en logisk SQL-Server och-databas. Observera att ett explicit beroende har definierats mellan databasen och servern, även om databasen är underordnad servern.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "apiVersion": "2014-04-01-preview",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "apiVersion": "2014-04-01-preview",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "tags": {
          "displayName": "Database"
        },
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>referens-och list funktioner

[Funktionen Reference](template-functions-resource.md#reference) gör det möjligt för ett uttryck att härleda sitt värde från andra JSON-namn och värdepar eller körnings resurser. [List * Functions](template-functions-resource.md#list) returnerar värden för en resurs från en List åtgärd.  Referens-och list uttryck deklarerar implicit att en resurs är beroende av en annan, när den refererade resursen distribueras i samma mall och kallas för dess namn (inte resurs-ID). Om du skickar resurs-ID: t till referens-eller List funktionerna skapas inte en implicit referens.

Det allmänna formatet för referens funktionen är:

```json
reference('resourceName').propertyPath
```

Det allmänna formatet för Listnycklar-funktionen är:

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

Du kan använda antingen det här elementet eller dependsOn-elementet för att ange beroenden, men du behöver inte använda båda för samma beroende resurs. När det är möjligt bör du använda en implicit referens för att undvika att lägga till ett onödigt beroende.

Mer information finns i [referens funktion](template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Cirkulära beroenden

Resource Manager identifierar cirkulära beroenden vid verifiering av mall. Om du får ett fel meddelande om att det finns ett cirkulärt beroende, utvärderar du din mall för att se om några beroenden inte behövs och kan tas bort. Om det inte fungerar att ta bort beroenden kan du undvika cirkulära beroenden genom att flytta några distributions åtgärder till underordnade resurser som distribueras efter de resurser som har cirkulärt beroende. Anta till exempel att du distribuerar två virtuella datorer, men du måste ange egenskaper för var och en som refererar till den andra. Du kan distribuera dem i följande ordning:

1. vm1
2. VM2
3. Tillägget på VM1 är beroende av VM1 och VM2. Tillägget anger värden för VM1 som hämtas från VM2.
4. Tillägget på VM2 är beroende av VM1 och VM2. Tillägget anger värden för VM2 som hämtas från VM1.

Information om hur du bedömer distributions ordningen och löser beroende fel finns i [Felsöka vanliga problem med Azure-distributioner med Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom självstudierna finns i [Självstudier: skapa Azure Resource Manager mallar med beroende resurser](template-tutorial-create-templates-with-dependent-resources.md).
* En Microsoft Learn-modul som täcker resurs beroenden finns i [hantera komplexa moln distributioner med hjälp av avancerade funktioner för ARM-mallar](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Rekommendationer vid inställning av beroenden finns i [metod tips för Azure Resource Manager mall](template-best-practices.md).
* Information om hur du felsöker beroenden under distributionen finns i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](common-deployment-errors.md).
* Mer information om hur du skapar Azure Resource Manager-mallar finns i [Redigera mallar](template-syntax.md).
* En lista över tillgängliga funktioner i en mall finns i [Template Functions](template-functions.md).

