---
title: Mallens struktur och syntax
description: Beskriver strukturen och egenskaperna för Azure Resource Manager mallar (ARM-mallar) med deklarativ JSON-syntax.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: da64eb8abeaf45f58933dfbddaf954cad8e66f4a
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120424"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Förstå strukturen och syntaxen för ARM-mallar

I den här artikeln beskrivs strukturen för en Azure Resource Manager-mall (ARM-mall). Den visar de olika avsnitten i en mall och de egenskaper som är tillgängliga i dessa avsnitt.

Den här artikeln är avsedd för användare som har en viss välbekanthet med ARM-mallar. Den innehåller detaljerad information om mallens struktur. En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i [Självstudier: skapa och distribuera din första arm-mall](template-tutorial-create-first-template.md). Om du vill lära dig mer om ARM-mallar via en guidad uppsättning moduler på Microsoft Learn, se [distribuera och hantera resurser i Azure med hjälp av arm-mallar](/learn/paths/deploy-manage-resource-manager-templates/).

## <a name="template-format"></a>Mallformat

I sin enklaste struktur har en mall följande element:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| $schema |Ja |Platsen för den JavaScript Object Notation (JSON) schema fil som beskriver versionen av mallens språk. Vilket versionsnummer du använder beror på distributionens omfattning och JSON-redigeraren.<br><br>Om du använder [Visual Studio Code med Azure Resource Manager Tools-tillägget](quickstart-create-templates-use-visual-studio-code.md)använder du den senaste versionen för resurs grupps distributioner:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Andra redigerare (inklusive Visual Studio) kanske inte kan bearbeta schemat. För dessa redigerare använder du:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>För prenumerations distributioner använder du:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>För distributioner av hanterings grupper använder du:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>För klient distributioner använder du:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |Ja |Version av mallen (till exempel 1.0.0.0). Du kan ange valfritt värde för det här elementet. Använd det här värdet om du vill dokumentera viktiga ändringar i mallen. När du distribuerar resurser med hjälp av mallen kan det här värdet användas för att se till att rätt mall används. |
| apiProfile |Inga | En API-version som fungerar som en samling av API-versioner för resurs typer. Använd det här värdet för att undvika att behöva ange API-versioner för varje resurs i mallen. När du anger en API-profil version och inte anger en API-version för resurs typen, använder Resource Manager API-versionen för den resurs typ som definieras i profilen.<br><br>Egenskapen API Profile är särskilt användbar när du distribuerar en mall till olika miljöer, till exempel Azure Stack och globala Azure. Använd API Profile-versionen för att kontrol lera att din mall automatiskt använder versioner som stöds i båda miljöerna. En lista över aktuella API-profiler och de resurs-API-versioner som definierats i profilen finns i [API-profil](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Mer information finns i [spåra versioner med hjälp av API-profiler](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [komponentparametrar](#parameters) |Inga |Värden som anges när distributionen körs för att anpassa resurs distributionen. |
| [användarvariabler](#variables) |Inga |Värden som används som JSON-fragment i mallen för att förenkla mallarnas språk uttryck. |
| [funktionen](#functions) |Inga |Användardefinierade funktioner som är tillgängliga i mallen. |
| [resurser](#resources) |Ja |Resurs typer som distribueras eller uppdateras i en resurs grupp eller prenumeration. |
| [utdata](#outputs) |Inga |Värden som returneras efter distribution. |

Varje element har egenskaper som du kan ange. I den här artikeln beskrivs mallens avsnitt mer detaljerat.

## <a name="parameters"></a>Parametrar

I `parameters` avsnittet i mallen anger du vilka värden som du kan ange när du distribuerar resurserna. Du kan ha högst 256 parametrar i en mall. Du kan minska antalet parametrar genom att använda objekt som innehåller flera egenskaper.

Tillgängliga egenskaper för en parameter är:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| parameter-Name |Ja |Parameterns namn. Måste vara ett giltigt JavaScript-ID. |
| typ |Ja |Typ av parameter värde. De tillåtna typerna och värdena är **String**, **SecureString**, **int**, **bool**, **Object**, **secureObject** och **array**. Se [data typer i arm-mallar](data-types.md). |
| Standar |Inga |Standardvärdet för parametern, om det inte finns något värde för parametern. |
| allowedValues |Inga |Matris med tillåtna värden för parametern för att kontrol lera att rätt värde har angetts. |
| minValue |Inga |Det minsta värdet för int-typ parametrar, detta värde är inkluderat. |
| maxValue |Inga |Det maximala värdet för int-typ parametrar, detta värde är inkluderat. |
| minLength |Inga |Den minsta längden för parametrar av typen sträng, säker sträng och mat ris typ, detta värde är inkluderat. |
| Max |Inga |Den maximala längden för parametrar av typen sträng, säker sträng och mat ris typ, detta värde är inkluderat. |
| beskrivning |Inga |Beskrivning av den parameter som visas för användarna via portalen. Mer information finns i [kommentarer i mallar](#comments). |

Exempel på hur du använder parametrar finns i [parametrar i arm-mallar](template-parameters.md).

## <a name="variables"></a>Variabler

I `variables` avsnittet skapar du värden som kan användas i hela mallen. Du behöver inte definiera variabler, men de fören klar ofta mallen genom att minska komplexa uttryck. Formatet för varje variabel matchar en av [data typerna](data-types.md).

I följande exempel visas tillgängliga alternativ för att definiera en variabel:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Information om hur `copy` du använder för att skapa flera värden för en variabel finns i [variabel iteration](copy-variables.md).

Exempel på hur du använder variabler finns i [variabler i arm-mallen](template-variables.md).

## <a name="functions"></a>Functions

I mallen kan du skapa egna funktioner. Dessa funktioner är tillgängliga för användning i din mall. Vanligt vis definierar du komplicerade uttryck som du inte vill upprepa i hela mallen. Du kan skapa användardefinierade funktioner från uttryck och [funktioner](template-functions.md) som stöds i mallar.

När du definierar en användar funktion finns det vissa begränsningar:

* Funktionen kan inte komma åt variabler.
* Funktionen kan bara använda parametrar som har definierats i funktionen. När du använder [funktionen parametrar](template-functions-deployment.md#parameters) i en användardefinierad funktion är du begränsad till parametrarna för den funktionen.
* Funktionen kan inte anropa andra användardefinierade funktioner.
* Funktionen kan inte använda funktionen [Reference](template-functions-resource.md#reference).
* Parametrar för funktionen kan inte ha standardvärden.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| namnområde |Ja |Namn område för anpassade funktioner. Används för att undvika namn konflikter med mall funktioner. |
| funktions namn |Ja |Namnet på den anpassade funktionen. När du anropar funktionen kombinerar du funktions namnet med namn området. Om du till exempel vill anropa en funktion som heter `uniqueName` i namn området Contoso använder du `"[contoso.uniqueName()]"` . |
| parameter-Name |Inga |Namnet på parametern som ska användas i den anpassade funktionen. |
| parameter-värde |Inga |Typ av parameter värde. De tillåtna typerna och värdena är **String**, **SecureString**, **int**, **bool**, **Object**, **secureObject** och **array**. |
| typ av utdata |Ja |Typ av utmatnings värde. Utmatnings värden har stöd för samma typer som indataparametrarna för funktionen. |
| utmatnings värde |Ja |Mallens språk uttryck som utvärderas och returneras från funktionen. |

Exempel på hur du använder anpassade funktioner finns i [användardefinierade funktioner i arm-mallen](template-user-defined-functions.md).

## <a name="resources"></a>Resurser

I `resources` avsnittet definierar du de resurser som distribueras eller uppdateras.

Du definierar resurser med följande struktur:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| moduletype | Inga | Booleskt värde som anger om resursen ska tillhandahållas under distributionen. När `true` skapas resursen under distributionen. När `false` hoppas resursen över för den här distributionen. Se [villkor](conditional-resource-deployment.md). |
| typ |Ja |Typ av resurs. Det här värdet är en kombination av resurs leverantörens namn område och resurs typ (till exempel `Microsoft.Storage/storageAccounts` ). Information om vilka värden som är tillgängliga finns i [referens för mallar](/azure/templates/). För en underordnad resurs är formatet för typen beroende av om den är kapslad i den överordnade resursen eller definieras utanför den överordnade resursen. Se [Ange namn och typ för underordnade resurser](child-resource-name-type.md). |
| apiVersion |Ja |Den version av REST API som ska användas för att skapa resursen. När du skapar en ny mall ställer du in det här värdet till den senaste versionen av resursen som du distribuerar. Så länge mallen fungerar vid behov, Fortsätt att använda samma API-version. Genom att fortsätta använda samma API-version minimerar du risken för att en ny API-version ändrar hur mallen fungerar. Överväg att bara uppdatera API-versionen när du vill använda en ny funktion som introduceras i en senare version. Information om vilka värden som är tillgängliga finns i [referens för mallar](/azure/templates/). |
| name |Ja |Namn på resursen. Namnet måste följa de URI-komponentparametrar som definierats i RFC3986. Azure-tjänster som visar resurs namnet för utomstående parter verifierar namnet för att se till att det inte är ett försök att använda en annan identitet. För en underordnad resurs är formatet på namnet beroende av om det är kapslat i den överordnade resursen eller definierats utanför den överordnade resursen. Se [Ange namn och typ för underordnade resurser](child-resource-name-type.md). |
| Comment |Inga |Dina anteckningar om att dokumentera resurserna i mallen. Mer information finns i [kommentarer i mallar](template-syntax.md#comments). |
| location |Det varierar |Geo-platser som stöds för den angivna resursen. Du kan välja någon av de tillgängliga platserna, men vanligt vis är det bra att välja en som är nära dina användare. Vanligt vis är det också bra att placera resurser som interagerar med varandra i samma region. De flesta resurs typer kräver en plats, men vissa typer (till exempel en roll tilldelning) kräver ingen plats. Se [Ange resurs plats](resource-location.md). |
| dependsOn |Inga |Resurser som måste distribueras innan den här resursen distribueras. Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i rätt ordning. När resurserna inte är beroende av varandra distribueras de parallellt. Värdet kan vara en kommaavgränsad lista över resurs namn eller resurs unika identifierare. Endast List resurser som har distribuerats i den här mallen. Resurser som inte har definierats i den här mallen måste redan finnas. Undvik att lägga till onödiga beroenden eftersom de kan minska distributionen och skapa cirkulära beroenden. Vägledning om hur du ställer in beroenden finns i [definiera ordningen för att distribuera resurser i arm-mallar](define-resource-dependency.md). |
| tags |Inga |Taggar som är associerade med resursen. Använd taggar för att logiskt organisera resurser i din prenumeration. |
| sku | Inga | Vissa resurser tillåter värden som definierar SKU: n som ska distribueras. Du kan till exempel ange typen av redundans för ett lagrings konto. |
| metod | Inga | Vissa resurser tillåter ett värde som definierar vilken typ av resurs du distribuerar. Du kan till exempel ange vilken typ av Cosmos DB som ska skapas. |
| kopiering |Inga |Om fler än en instans behövs, antalet resurser som ska skapas. Standard läget är parallellt. Ange serie läge när du inte vill att alla eller resurserna ska distribueras samtidigt. Mer information finns i [skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md). |
| planera | Inga | Vissa resurser tillåter värden som definierar den plan som ska distribueras. Du kan till exempel ange Marketplace-avbildningen för en virtuell dator. |
| properties |Inga |Resurs-/regionsspecifika konfigurations inställningar. Värdena för egenskaperna är desamma som de värden som du anger i begär ande texten för åtgärden REST API (metoden sätt) för att skapa resursen. Du kan också ange en kopierad matris för att skapa flera instanser av en egenskap. Information om vilka värden som är tillgängliga finns i [referens för mallar](/azure/templates/). |
| resources |Inga |Underordnade resurser som är beroende av den resurs som definieras. Ange endast resurs typer som tillåts av schemat för den överordnade resursen. Beroendet av den överordnade resursen är inte underförstådd. Du måste uttryckligen definiera det beroendet. Se [Ange namn och typ för underordnade resurser](child-resource-name-type.md). |

## <a name="outputs"></a>Utdata

I `outputs` avsnittet anger du värden som returneras från distributionen. Normalt returnerar du värden från resurser som har distribuerats.

I följande exempel visas strukturen för en utmatnings definition:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| utmatnings namn |Ja |Namn på utdata. Måste vara ett giltigt JavaScript-ID. |
| moduletype |Inga | Booleskt värde som anger om det här värdet returneras. När `true` är värdet inkluderat i utdata för distributionen. När `false` ignoreras värdet för utdata för den här distributionen. Om inget värde anges är standardvärdet `true` . |
| typ |Ja |Typ av utmatnings värde. Utmatnings värden har stöd för samma typer som indataparametrarna för mallar. Om du anger **SecureString** för utdatatypen visas inte värdet i distributions historiken och kan inte hämtas från en annan mall. Om du vill använda ett hemligt värde i fler än en mall lagrar du hemligheten i en Key Vault och refererar till hemligheten i parameter filen. Mer information finns i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](key-vault-parameter.md). |
| värde |Inga |Mallens språk uttryck som utvärderas och returneras som utdata-värde. Ange antingen **Value** eller **copy**. |
| kopiering |Inga | Används för att returnera fler än ett värde för utdata. Ange **värde** eller **Kopiera**. Mer information finns i [utdata iteration i arm-mallar](copy-outputs.md). |

Exempel på hur du använder utdata finns i [utdata i arm-mallen](template-outputs.md).

<a id="comments"></a>

## <a name="comments-and-metadata"></a>Kommentarer och metadata

Du har några alternativ för att lägga till kommentarer och metadata till din mall.

### <a name="comments"></a>Kommentarer

För infogade kommentarer kan du använda antingen `//` eller, `/* ... */` men den här syntaxen fungerar inte med alla verktyg. Om du lägger till den här typen av kommentar måste du se till att de verktyg du använder stöder infogade JSON-kommentarer.

> [!NOTE]
> Om du vill distribuera mallar med kommentarer med hjälp av Azure CLI med version 2.3.0 eller äldre måste du använda `--handle-extended-json-format` växeln.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

I Visual Studio Code kan [Azure Resource Manager Tools-tillägget](quickstart-create-templates-use-visual-studio-code.md) automatiskt identifiera en arm-mall och ändra språk läget. Om du ser **Azure Resource Manager mall** i det nedre högra hörnet av Visual Studio Code kan du använda de infogade kommentarerna. De infogade kommentarerna markeras inte längre som ogiltiga.

![Visual Studio Code Azure Resource Manager Template mode](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadata

Du kan lägga till ett `metadata` objekt nästan var som helst i mallen. Resource Manager ignorerar objektet, men JSON-redigeraren kan varna dig om att egenskapen inte är giltig. I objektet definierar du de egenskaper du behöver.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

För `parameters` lägger du till ett `metadata` objekt med en `description` egenskap.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

När du distribuerar mallen via portalen används den text som du anger i beskrivningen automatiskt som ett tips för den parametern.

![Visa parameter tips](./media/template-syntax/show-parameter-tip.png)

För `resources` lägger du till ett `comments` element eller ett `metadata` objekt. I följande exempel visas både ett- `comments` element och ett- `metadata` objekt.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

För `outputs` lägger du till ett `metadata` objekt till utdata-värdet.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Det går inte att lägga till ett `metadata` objekt i användardefinierade funktioner.

## <a name="multi-line-strings"></a>Strängar med flera rader

Du kan dela upp en sträng i flera rader. Se till exempel `location` egenskapen och en av kommentarerna i följande JSON-exempel.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

> [!NOTE]
> Om du vill distribuera mallar med strängar med flera rader med hjälp av Azure CLI med version 2.3.0 eller äldre måste du använda `--handle-extended-json-format` växeln.

## <a name="next-steps"></a>Nästa steg

* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om vilka funktioner du kan använda i en mall finns i [funktioner i arm-mallar](template-functions.md).
* Information om hur du kombinerar flera mallar under distributionen finns i [använda länkade och kapslade mallar när du distribuerar Azure-resurser](linked-templates.md).
* Rekommendationer om hur du skapar mallar finns i [metod tips för ARM-mallar](template-best-practices.md).
* Svar på vanliga frågor finns [vanliga frågor om ARM-mallar](frequently-asked-questions.md).
