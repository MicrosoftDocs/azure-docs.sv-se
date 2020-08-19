---
title: Översikt – Automatisera distribution för Azure Logic Apps
description: Lär dig mer om Azure Resource Manager mallar för att automatisera distributionen av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 391692d708adbd542b2cf358f0ac597dc1db3fa0
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565561"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Översikt: Automatisera distribution av Azure Logic Apps med hjälp av Azure Resource Manager mallar

När du är redo att automatisera skapandet och distributionen av din Logic app kan du expandera din Logic Apps underliggande arbets flödes definition till en [Azure Resource Manager-mall](../azure-resource-manager/management/overview.md). Den här mallen definierar infrastruktur, resurser, parametrar och annan information för etablering och distribution av din Logic app. Genom att definiera parametrar för värden som varierar vid distribution, även kallat *parametriserade*, kan du upprepade gånger och distribuera Logi Kap par baserat på olika distributions behov.

Om du till exempel distribuerar till miljöer för utveckling, testning och produktion använder du förmodligen olika anslutnings strängar för varje miljö. Du kan deklarera mallparametrar som accepterar olika anslutnings strängar och sedan lagra dessa strängar i en separat [parameter fil](../azure-resource-manager/templates/parameter-files.md). På så sätt kan du ändra dessa värden utan att behöva uppdatera och distribuera om mallen. För scenarier där du har parameter värden som är känsliga eller måste skyddas, till exempel lösen ord och hemligheter, kan du lagra dessa värden i [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) och låta parameter filen hämta dessa värden. I dessa scenarier kan du dock omdistribuera för att hämta de aktuella värdena.

Den här översikten beskriver attributen i en Resource Manager-mall som innehåller en arbets flödes definition för Logic app. Både mallen och din arbets flödes definition använder JSON-syntax, men det finns vissa skillnader eftersom arbets flödes definitionen också följer [språk schema för arbets flödes definitionen](../logic-apps/logic-apps-workflow-definition-language.md). Mall uttryck och definitioner för arbets flödes definitioner skiljer sig till exempel från att [referera till parametrar](#parameter-references) och de värden som de kan acceptera.

> [!TIP]
> För det enklaste sättet att hämta en giltig mall för parametriserade appar som oftast är redo för distribution, använder du Visual Studio (kostnads fri Community Edition eller senare) och Azure Logic Apps verktyg för Visual Studio. Du kan sedan antingen [skapa din Logi Kap par i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) eller [söka efter och ladda ned en befintlig Logic app från Azure till Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> Du kan också skapa Logic app-mallar genom [att använda Azure PowerShell med LogicAppTemplate-modulen](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

I exempel Logic-appen i det här avsnittet används en [Office 365 Outlook-utlösare](/connectors/office365/) som utlöses när ett nytt e-postmeddelande tas emot och en [Azure Blob Storage-åtgärd](/connectors/azureblob/) som skapar en BLOB för e-postmeddelandet och laddar upp denna blob till en Azure Storage-behållare. I exemplen visas också hur du Parameterisera värden som varierar vid distribution.

Mer information om Resource Manager-mallar finns i följande avsnitt:

* [Azure Resource Manager mallens struktur och syntax](../azure-resource-manager/templates/template-syntax.md)
* [Metodtips för Azure Resource Manager-mall](../azure-resource-manager/templates/template-best-practices.md)
* [Utveckla Azure Resource Manager-mallar för molnkonsekvens](../azure-resource-manager/templates/templates-cloud-consistency.md)

Exempel på exempel på Logic app-mallar finns i följande exempel:

* [Fullständig mall](#full-example-template) som används för det här ämnets exempel
* [Exempel på snabb starts Logic app Template](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) i GitHub

Information om mall resurs information som är unik för logi Kap par, integrations konton och integrations konto artefakter finns i [resurs typer för Microsoft. Logic](/azure/templates/microsoft.logic/allversions).

<a name="template-structure"></a>

## <a name="template-structure"></a>Mallstruktur

På den högsta nivån följer en Resource Manager-mall den här strukturen, som beskrivs fullständigt i avsnittet [Azure Resource Manager mall struktur och syntax](../azure-resource-manager/templates/template-syntax.md) :

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

För en Logic app-mall arbetar du främst med följande mall-objekt:

| Attribut | Beskrivning |
|-----------|-------------|
| `parameters` | Deklarerar [mallparametrar för att](../azure-resource-manager/templates/template-syntax.md#parameters) acceptera värdena som ska användas när du skapar och anpassar resurser för distribution i Azure. Dessa parametrar accepterar till exempel värdena för din Logic Apps namn och plats, anslutningar och andra resurser som krävs för distribution. Du kan lagra dessa parameter värden i en [parameter fil](#template-parameter-files), som beskrivs senare i det här avsnittet. Allmän information finns i [parametrar-Resource Manager-mallens struktur och syntax](../azure-resource-manager/templates/template-syntax.md#parameters). |
| `resources` | Definierar de [resurser](../azure-resource-manager/templates/template-syntax.md#resources) som ska skapas eller uppdateras och distribueras till en Azure-resurs grupp, till exempel din Logi Kap par, anslutningar, Azure Storage-konton och så vidare. Allmän information finns i [resurser – Resource Manager-mallens struktur och syntax](../azure-resource-manager/templates/template-syntax.md#resources). |
||||

Din Logic app-mall använder detta fil namns format:

**<*Logic – App-Name* # C0.jspå**

> [!IMPORTANT]
> Mallens syntax är Skift läges känslig så se till att du använder konsekvent Skift läge. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Mallparametrar

En Logic app-mall har flera `parameters` objekt som finns på olika nivåer och utför olika funktioner. På den översta nivån kan du till exempel deklarera [mallparametrar](../azure-resource-manager/templates/template-syntax.md#parameters) för de värden som ska accepteras och användas vid distribution när du skapar och distribuerar resurser i Azure, till exempel:

* Din Logic app
* Anslutningar som din logik använder för att komma åt andra tjänster och system via [hanterade anslutningar](../connectors/apis-list.md)
* Andra resurser som din Logic app behöver för distribution

  Om din Logic app t. ex. använder ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) för B2B-scenarier (Business-to-Business), `parameters` deklarerar mallens översta objekt den parameter som godkänner resurs-ID: t för det integrations kontot.

Här är den allmänna strukturen och syntaxen för en parameter definition, som beskrivs fullständigt av [parametrarna-Resource Manager-mallens struktur och syntax](../azure-resource-manager/templates/template-syntax.md#parameters):

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

Det här exemplet visar bara mallparametrar för de värden som används för att skapa och distribuera dessa resurser i Azure:

* Namn och plats för din Logic app
* ID som ska användas för ett integrations konto som är länkat till Logic app

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Förutom parametrar som hanterar värden som är känsliga eller måste skyddas, t. ex. användar namn, lösen ord och hemligheter, inkluderar alla dessa parametrar `defaultValue` attribut, men i vissa fall är standardvärdena tomma värden. De distributions värden som ska användas för dessa mallparametrar tillhandahålls av exempel [parameter filen](#template-parameter-files) som beskrivs senare i det här avsnittet.

Mer information om hur du skyddar mallparametrar finns i följande avsnitt:

* [Säkerhets rekommendationer för mallparametrar](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Förbättra säkerheten för mallparametrar](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Skicka skyddade parameter värden med Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Andra mallfiler refererar ofta till mallparametrar så att de kan använda de värden som passerar genom mallparametrar, till exempel:

* [Mallens resurser-objekt](#template-resources), som beskrivs längre fram i det här avsnittet, definierar varje resurs i Azure som du vill skapa och distribuera, till exempel din [Logic Apps resurs definition](#logic-app-resource-definition). Dessa resurser använder ofta mallparametrar, till exempel namn och plats och anslutnings information för din Logic Apps.

* På en djupare nivå i din Logi Kap par resurs definition, deklarerar din [arbets flödes definition](#workflow-definition-parameters) parametrar-objektet parametrar för de värden som ska användas i din Logic Apps-körning. Du kan till exempel deklarera parametrar för arbets flödes definitioner för användar namn och lösen ord som en HTTP-utlösare använder för autentisering. Om du vill ange värden för parametrarna för arbets flödes definitioner använder du `parameters` objektet *utanför* arbets flödes definitionen, men fortfarande *i* din Logic Apps resurs definition. I det här yttre `parameters` objektet kan du referera till tidigare deklarerade mallparametrar som kan acceptera värden vid distribution från en parameter fil.

När du refererar till parametrar använder mallar och funktioner olika syntax och beter sig annorlunda än uttryck och funktioner för arbets flödes definition. Mer information om dessa skillnader finns i [referenser till parametrar](#parameter-references) senare i det här avsnittet.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Metod tips – mallparametrar

Här följer några metod tips för att definiera parametrar:

* Deklarera bara parametrar för värden som varierar beroende på dina distributions behov. Deklarera inte parametrar för värden som ligger på samma sätt för olika distributions krav.

* Inkludera `defaultValue` attributet, som kan ange tomma värden, för alla parametrar förutom värden som är känsliga eller måste skyddas. Använd alltid säkra parametrar för användar namn, lösen ord och hemligheter. Om du vill dölja eller skydda känsliga parameter värden följer du anvisningarna i följande avsnitt:

  * [Säkerhets rekommendationer för mallparametrar](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Förbättra säkerheten för mallparametrar](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Skicka skyddade parameter värden med Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Om du vill särskilja mallens parameter namn från parameter namn för arbets flödes definitioner kan du använda beskrivande mall parameter namn, till exempel: `TemplateFabrikamPassword`

Mer metod tips finns i [metod tips för](../azure-resource-manager/templates/template-best-practices.md#parameters)mallparametrar.

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Mall parametrar fil

Om du vill ange värden för mallparametrar lagrar du dessa värden i en [parameter fil](../azure-resource-manager/templates/parameter-files.md). På så sätt kan du använda olika parametrar-filer baserat på dina distributions behov. Här är fil namns formatet som ska användas:

* Logic app-mallens fil namn: ** < *Logic – App-Name* # C0.jspå**
* Parametrar fil namn: ** < *Logic – App-Name* # C0.parameters.jspå**

Här är strukturen i parameter filen, som innehåller en nyckel valv referens för att [Skicka ett skyddat parameter värde med Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md):

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

I den här exempel parameter filen anges värdena för de mallparametrar som har deklarerats tidigare i det här avsnittet:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Mallresurser

Din mall har ett `resources` -objekt, som är en matris som innehåller definitioner för varje resurs för att skapa och distribuera i Azure, till exempel din Logi Kap par [resurs definition](#logic-app-resource-definition), [anslutnings resurs definitioner](#connection-resource-definitions)och andra resurser som din Logic app behöver för att distribuera.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Mallar kan innehålla resurs definitioner för flera Logi Kap par, så se till att alla dina Logic app-resurser anger samma Azure-resurs grupp. När du distribuerar mallen till en Azure-resurs grupp med hjälp av Visual Studio, uppmanas du att ange vilken logisk app som du vill öppna. Azures resurs grupps projekt kan dessutom innehålla mer än en mall, så se till att du väljer rätt parameter fil när du uppmanas till det.

<a name="view-resource-definitions"></a>

### <a name="view-resource-definitions"></a>Visa resurs definitioner

Om du vill granska resurs definitionerna för alla resurser i en Azure-resurs grupp kan du antingen [Ladda ned din Logic app från Azure till Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), vilket är det enklaste sättet att skapa en giltig mall för parametriserade appar som är redo för distribution eller följ stegen i Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. Hitta Azure-resurs gruppen som innehåller din Logi Kap par, dina anslutningar och andra resurser.

1. I verktygsfältet resurs grupp väljer du **Översikt**och väljer sedan alla resurser i resurs gruppen.

1. I verktygsfältet resurs grupp under **Inställningar**väljer du **Exportera mall**.

   Portalen visar definitionerna för de resurser som du har valt. Mer information finns i [en och flera-resurs-export till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md).

Allmän information om mallens resurser och deras attribut finns i följande avsnitt:

* [Resurser – Resource Manager-mall struktur och syntax](../azure-resource-manager/templates/template-syntax.md#resources)
* [Metod tips för mall-resurser](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Resurs definition för Logic app

Din Logi Kap par resurs definition börjar med `properties` objektet, som innehåller den här informationen:

* Din Logic Apps tillstånd vid distribution
* ID för alla integrations konton som används av din Logic app
* Din Logic Apps arbets flödes definition
* Ett `parameters` objekt som anger de värden som ska användas vid körning
* Annan resursinformation om din Logic app, till exempel namn, typ, plats och så vidare

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Här följer de attribut som är speciella för din resurs definition för Logic app:

| Attribut | Krävs | Typ | Beskrivning |
|-----------|----------|------|-------------|
| `state` | Ja | Sträng | Din Logi Kap par status vid distribution där innebär att din Logi Kap par `Enabled` är Live och `Disabled` innebär att din Logic app är inaktiv. Om du till exempel inte är redo för din Logi Kap par, men vill distribuera ett utkast till en version, kan du använda `Disabled` alternativet. |
| `integrationAccount` | No | Objekt | Om din Logic app använder ett integrations konto, som lagrar artefakter för Business-to-Business (B2B)-scenarier, inkluderar det här objektet `id` attributet, som anger ID: t för integrations kontot. |
| `definition` | Yes | Objekt | Din Logic Apps-underliggande arbets flödes definition, som är samma objekt som visas i kodvyn och beskrivs fullständigt i avsnittet [schema referens för språk för arbets flödes definition](../logic-apps/logic-apps-workflow-definition-language.md) . I den här arbets flödes definitionen `parameters` deklarerar objektet parametrar för de värden som ska användas vid Logic app Runtime. Mer information finns i [arbets flödes definitioner och parametrar](#workflow-definition-parameters). <p><p>Om du vill visa attributen i din Logic Apps arbets flödes definition växlar du från "designvyn" till "kodvyn" i Azure Portal eller Visual Studio, eller genom att använda ett verktyg som [Azure Resource Explorer](https://resources.azure.com). |
| `parameters` | No | Objekt | [Parameter värden för arbets flödes definition](#workflow-definition-parameters) som ska användas vid Logic app Runtime. Parameter definitionerna för dessa värden visas i [arbets flödes definitionens](#workflow-definition-parameters)Parameters-objekt. Om din Logic app använder [hanterade anslutningar](../connectors/apis-list.md) för att komma åt andra tjänster och system, innehåller det här objektet dessutom ett `$connections` objekt som anger de anslutnings värden som ska användas vid körning. |
| `accessControl` | No | Objekt | För att ange säkerhetsattribut för din Logi Kap par, till exempel att begränsa IP-åtkomsten till begär ande utlösare eller köra tidigare indata och utdata. Mer information finns i [säker åtkomst till Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md). |
||||

Information om mall resurs information som är unik för logi Kap par, integrations konton och integrations konto artefakter finns i [resurs typer för Microsoft. Logic](/azure/templates/microsoft.logic/allversions).

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Arbets flödes definition och parametrar

Din Logic Apps arbets flödes definition visas i `definition` objektet, som visas i `properties` objektet i din Logic Apps resurs definition. Det här `definition` objektet är samma objekt som visas i kodvyn och beskrivs fullständigt i avsnittet [schema referens för språk för arbets flödes definition](../logic-apps/logic-apps-workflow-definition-language.md) . Arbets flödes definitionen innehåller ett inre `parameters` deklarations objekt där du kan definiera nya eller redigera befintliga parametrar för de värden som används av arbets flödes definitionen vid körning. Du kan sedan referera till dessa parametrar i utlösaren eller åtgärderna i arbets flödet. Som standard är det här `parameters` objektet tomt om inte din Logic app skapar anslutningar till andra tjänster och system via [hanterade anslutningar](../connectors/apis-list.md).

Om du vill ange värden för parametrarna för arbets flödes definitioner använder du `parameters` objektet *utanför* arbets flödes definitionen, men fortfarande *i* din Logic Apps resurs definition. I det här yttre `parameters` objektet kan du referera till tidigare deklarerade mallparametrar som kan acceptera värden vid distribution från en parameter fil.

> [!TIP]
>
> Vi rekommenderar att du inte direkt refererar till mallparametrar, som utvärderas vid distribution, inifrån arbets flödes definitionen. Deklarera i stället en parameter för arbets flödes definition, som du sedan kan ange i `parameters` objektet som *ligger utanför* arbets flödes definitionen, men fortfarande *i* din Logic Apps resurs definition. Mer information finns i [referenser till parametrar](#parameter-references).

Den här syntaxen visar var du kan deklarera parametrar på både mall-och arbets flödets definitions nivå, där du kan ange dessa parameter värden genom att referera till mall-och arbets flödets definitions parametrar:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Parametrar för säker arbets flödes definition

För en arbets flödes definitions parameter som hanterar känslig information, lösen ord, åtkomst nycklar eller hemligheter vid körning, deklarera eller redigera parametern för att använda `securestring` `secureobject` parameter typen eller. Du kan referera till den här parametern i hela arbets flödes definitionen. Deklarera en parameter som har samma typ för att hantera den här informationen vid distribution på mallens översta nivå.

Ange värdet för parametern för arbets flödes definition genom att använda det `parameters` objekt som *ligger utanför* arbets flödes definitionen, men fortfarande *i* din resurs definition för Logic app för att referera till parametern Template. Slutligen, för att skicka värdet till din mallparameter vid distribution, lagrar du värdet i [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) och refererar till nyckel valvet i [parameter filen](#template-parameter-files) som används av mallen vid distribution.

Den här exempel mal len visar hur du kan utföra dessa uppgifter genom att definiera säkra parametrar vid behov så att du kan lagra värdena i Azure Key Vault:

* Deklarera skyddade parametrar för de värden som används för att autentisera åtkomst.
* Använd de här värdena både i nivåerna mall och arbets flödes definition.
* Ange dessa värden med hjälp av en parameter fil.

**Mall**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Parameter fil**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Metod tips – parametrar för arbets flödes definition

För att se till att Logic App Designer kan visa parametrar för arbets flödes definition, följer du dessa rekommendationer:

* Inkludera `defaultValue` attributet, som kan ange tomma värden, för alla parametrar förutom värden som är känsliga eller måste skyddas.

* Använd alltid säkra parametrar för användar namn, lösen ord och hemligheter. Om du vill dölja eller skydda känsliga parameter värden följer du anvisningarna i följande avsnitt:

  * [Säkerhets rekommendationer för åtgärds parametrar](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Säkerhets rekommendationer för parametrar i arbets flödes definitioner](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Överför säkra parameter värden med Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Mer information om parametrar för arbets flödes definitioner finns i [parametrar-definitions språk för arbets flöde](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Anslutnings resurs definitioner

När din Logi Kap par skapar och använder anslutningar till andra tjänster och system med hjälp av [hanterade anslutningar](../connectors/apis-list.md), `resources` innehåller mallens objekt resurs definitionerna för dessa anslutningar.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

Anslutnings resurs definitioner hänvisar till mallens toppnivå parametrar för sina värden, vilket innebär att du kan ange dessa värden vid distributionen genom att använda en parameter fil. Se till att anslutningarna använder samma Azure-resurs grupp och plats som din Logic app.

Här är en exempel resurs definition för en Office 365 Outlook-anslutning och motsvarande mallparametrar:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

Din Logic Apps resurs definition fungerar också med anslutnings resurs definitioner på följande sätt:

* Inuti arbets flödes definitionen `parameters` deklarerar objektet en `$connections` parameter för de anslutnings värden som ska användas vid Logic app Runtime. Utlösaren eller åtgärden som skapar en anslutning använder också motsvarande värden som passerar genom den här `$connections` parametern.

* *Utanför* arbets flödes definitionen men fortfarande *i* din Logic Apps resurs definition anger ett annat `parameters` objekt de värden som ska användas vid körning för `$connections` parametern genom att referera till motsvarande mallparametrar. Dessa värden använder mall-uttryck för att referera till resurser som lagrar metadata på ett säkert sätt för anslutningarna i din Logic app.

  Metadata kan till exempel innehålla anslutnings strängar och åtkomsttoken, som du kan lagra i [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Om du vill överföra dessa värden till dina mallparametrar refererar du till nyckel valvet i den [parameter fil](#template-parameter-files) som används av mallen vid distribution. Mer information om skillnader i referens parametrar finns i [referenser till parametrar](#parameter-references) senare i det här avsnittet.

  När du öppnar din Logic Apps arbets flödes definition i kodvyn via Azure Portal eller Visual Studio, `$connections` visas objektet utanför arbets flödes definitionen, men på samma nivå. Den här ordningen i kodvyn gör dessa parametrar enklare att referera till när du uppdaterar arbets flödes definitionen manuellt:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* Din Logic Apps resurs definition har ett- `dependsOn` objekt som anger beroenden för de anslutningar som används av din Logic app.

Varje anslutning som du skapar har ett unikt namn i Azure. När du skapar flera anslutningar till samma tjänst eller system läggs varje anslutnings namn till med ett nummer, som ökar med varje ny anslutning, till exempel, `office365` `office365-1` och så vidare.

Det här exemplet visar interaktioner mellan din Logic Apps resurs definition och en anslutnings resurs definition för Office 365 Outlook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Säkra anslutnings parametrar

För en anslutnings parameter som hanterar känslig information, lösen ord, åtkomst nycklar eller hemligheter, innehåller anslutningens resurs definition ett `parameterValues` objekt som anger de här värdena i namn värde par formatet. Om du vill dölja den här informationen kan du deklarera eller Redigera mallparametrar för dessa värden med hjälp av `securestring` `secureobject` parameter typerna eller. Du kan sedan lagra informationen i [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Om du vill överföra dessa värden till dina mallparametrar refererar du till nyckel valvet i den [parameter fil](#template-parameter-files) som används av mallen vid distribution.

Här är ett exempel som innehåller konto namnet och åtkomst nyckeln för en Azure Blob Storage-anslutning:

**Parameter fil**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Mall**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Autentisera anslutningar

Efter distributionen fungerar din Logic app från slut punkt till slut punkt med giltiga parametrar. Du måste dock fortfarande auktorisera alla OAuth-anslutningar för att generera giltiga åtkomsttoken för [autentisering av dina autentiseringsuppgifter](../active-directory/develop/authentication-vs-authorization.md). Mer information finns i [auktorisera OAuth-anslutningar](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Vissa anslutningar stöder användning av en Azure Active Directory (Azure AD) [tjänstens huvud namn](../active-directory/develop/app-objects-and-service-principals.md) för att auktorisera anslutningar för en logisk app som är [registrerad i Azure AD](../active-directory/develop/quickstart-register-app.md). Den här Azure Data Lake resurs definitionen för anslutning visar till exempel hur du refererar till mallparametrar som hanterar tjänstens huvud namns information och hur mallen deklarerar dessa parametrar:

**Resurs definition för anslutning**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Attribut | Beskrivning |
|-----------|-------------|
| `token:clientId` | Det program eller det klient-ID som är kopplat till tjänstens huvud namn |
| `token:clientSecret` | Det nyckel värde som är kopplat till tjänstens huvud namn |
| `token:TenantId` | Katalog-ID för din Azure AD-klient |
| `token:grantType` | Den begärda anslags typen, som måste vara `client_credentials` . Mer information finns i [Microsoft Identity Platform och OAuth 2,0-klientens autentiseringsuppgifter Flow](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Mall parameter definitioner**

Mallens översta nivå- `parameters` objekt deklarerar dessa parametrar för exempel anslutningen:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Mer information om hur du arbetar med tjänstens huvud namn finns i följande avsnitt:

* [Skapa ett huvud namn för tjänsten med hjälp av Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)
* [Skapa ett huvud namn för Azure-tjänsten med hjälp av Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
* [Skapa ett huvud namn för tjänsten med ett certifikat med hjälp av Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Referenser till parametrar

Om du vill referera till mallparametrar kan du använda mallar med [mallar](../azure-resource-manager/templates/template-functions.md)som utvärderas vid distributionen. Mall uttryck använder hak paren tes (**[]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Om du vill referera till definitioner av arbets flödes definitioner använder du [språk uttryck och funktioner för arbets flödes definitioner](../logic-apps/workflow-definition-language-functions-reference.md)som utvärderas vid körning. Du kanske märker att vissa mallar och funktioner för arbets flödes definitioner har samma namn. Uttryck för arbets flödes definition börjar med symbolen "at" ( **@** ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Du kan skicka mallparametrar till din arbets flödes definition för din Logic app som ska användas vid körning. Undvik dock att använda mallparametrar, uttryck och syntax i arbets flödes definitionen eftersom Logic Apps designer inte stöder mall-element. Dessutom kan mallens syntax och uttryck komplicera din kod på grund av skillnader i när uttryck utvärderas.

Följ i stället dessa allmänna steg för att deklarera och referera till de parametrar för arbets flödes definition som ska användas vid körning, deklarera och referera till de mallparametrar som ska användas vid distributionen och ange de värden som ska skickas vid distributionen med hjälp av en parameter fil. Fullständig information finns i avsnittet om [arbets flödes definition och parametrar](#workflow-definition-parameters) ovan i det här avsnittet.

1. Skapa din mall och deklarera mallparametrar för de värden som ska accepteras och användas vid distributionen.

1. I arbets flödes definitionen deklarerar du parametrarna för de värden som ska accepteras och används vid körning. Du kan sedan referera till dessa värden i och i arbets flödes definitionen.

1. I `parameters` objektet som *ligger utanför* arbets flödes definitionen, men fortfarande *i* din Logic Apps resurs definition, anger du värdena för dina arbets flödes definitions parametrar genom att referera till motsvarande mallparametrar. På så sätt kan du skicka parameter värden för mallen till dina parametrar för arbets flödes definitionerna.

1. I parameter filen anger du de värden för din mall som ska användas vid distributionen.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Fullständig exempel mall

Här är exempel mal len för parametriserade som används i följande avsnitt:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa mallar för logikappar](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
