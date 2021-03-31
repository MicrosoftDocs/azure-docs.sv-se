---
title: Log Analytics arbets ytans data export i Azure Monitor (förhands granskning)
description: Med Log Analytics data export kan du kontinuerligt exportera data för markerade tabeller från din Log Analytics arbets yta till ett Azure Storage-konto eller Azure-Event Hubs som det samlas in.
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 02/07/2021
ms.openlocfilehash: ea33eff30e712c1597c3606d74cb6d56683211ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102615592"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Log Analytics arbets ytans data export i Azure Monitor (förhands granskning)
Med Log Analytics data export för arbets yta i Azure Monitor kan du kontinuerligt exportera data från valda tabeller i din Log Analytics arbets yta till ett Azure Storage-konto eller Azure-Event Hubs som det samlas in. Den här artikeln innehåller information om den här funktionen och hur du konfigurerar data export i dina arbets ytor.

## <a name="overview"></a>Översikt
När data export har kon figurer ATS för Log Analytics arbets ytan exporteras alla nya data som skickas till de valda tabellerna i arbets ytan automatiskt till ditt lagrings konto i varje timme för att lägga till blobbar eller till händelsehubben i nästan i real tid.

![Översikt över data export](media/logs-data-export/data-export-overview.png)

Alla data från inkluderade tabeller exporteras utan filter. När du till exempel konfigurerar en data export regel för *SecurityEvent* -tabellen, exporteras alla data som skickas till *SecurityEvent* -tabellen från konfigurations tiden.


## <a name="other-export-options"></a>Andra export alternativ
Log Analytics data export för arbets ytan exporterar kontinuerligt data från en Log Analytics arbets yta. Andra alternativ för att exportera data för specifika scenarier är följande:

- Schemalagd export från en logg fråga med hjälp av en Logic app. Detta liknar data export funktionen, men du kan skicka filtrerade eller aggregerade data till Azure Storage. Den här metoden kan vara beroende av [loggnings frågans gränser](../service-limits.md#log-analytics-workspaces), se [arkivera data från Log Analytics arbets yta till Azure Storage med hjälp av Logic app](logs-export-logic-app.md).
- Exportera till en lokal dator med hjälp av PowerShell-skript. Se [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="limitations"></a>Begränsningar

- Konfigurationen kan utföras med CLI-eller REST-begäranden för närvarande. Azure Portal eller PowerShell stöds inte än.
- ```--export-all-tables```Alternativet i CLI och rest stöds inte och kommer att tas bort. Du bör ange listan över tabeller i export regler uttryckligen.
- Tabeller som stöds är för närvarande begränsade i avsnittet [tabeller som stöds](#supported-tables) nedan. Anpassade logg tabeller stöds till exempel inte för närvarande.
- Om data export regeln innehåller en tabell som inte stöds kommer åtgärden att lyckas, men inga data exporteras för tabellen förrän tabellen har stöd för. 
- Om data export regeln innehåller en tabell som inte finns fungerar den med fel ```Table <tableName> does not exist in the workspace``` .
- Din Log Analytics arbets yta kan finnas i vilken region som helst, förutom följande:
  - Azure Government-regioner
  - Japan, västra
  - Södra Brasilien, östra
  - Östra Norge
  - Förenade Arabemiraten, norra
- Du kan skapa två export regler i en arbets yta – i kan vara en regel till händelsehubben och en regel för lagrings kontot.
- Mål lagrings kontot eller händelsehubben måste finnas i samma region som Log Analytics-arbetsytan.
- Namn på tabeller som ska exporteras får inte vara längre än 60 tecken för ett lagrings konto och högst 47 tecken till en Event Hub. Tabeller med längre namn exporteras inte.
- Stöd för att lägga till BLOB-stöd för Azure Data Lake Storage finns nu i [begränsad offentlig för hands version](https://azure.microsoft.com/updates/append-blob-support-for-azure-data-lake-storage-preview/)

## <a name="data-completeness"></a>Data fullständighet
Data exporten kommer att fortsätta att försöka skicka data i upp till 30 minuter om målet inte är tillgängligt. Om det fortfarande inte är tillgängligt efter 30 minuter tas data bort tills målet blir tillgängligt.

## <a name="cost"></a>Cost
Det finns för närvarande inga ytterligare avgifter för data export funktionen. Prissättningen för data export kommer att meddelas i framtiden och ett meddelande som tillhandahålls innan faktureringen påbörjas. Om du väljer att fortsätta använda data export efter meddelande perioden debiteras du enligt tillämplig taxa.

## <a name="export-destinations"></a>Exportera mål

### <a name="storage-account"></a>Lagringskonto
Data skickas till lagrings konton när de når Azure Monitor och lagras i Tim tillägg av blobar. Konfigurationen för data export skapar en behållare för varje tabell i lagrings kontot med namnet *am-* följt av namnet på tabellen. Tabellen *SecurityEvent* skulle till exempel skickas till en behållare med namnet *am-SecurityEvent*.

Lagrings kontots BLOB-sökväg är *WorkspaceResourceId =/Subscriptions/Subscription-ID/ResourceGroups/ \<resource-group\> /providers/Microsoft.operationalinsights/workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d = \<two-digit numeric day\> /h = \<two-digit 24-hour clock hour\> /m = 00/PT1H.jspå*. Eftersom bifogade blobbar är begränsade till 50 000 skrivningar i lagringen kan antalet exporterade blobbar utökas om antalet tillägg är högt. Namngivnings mönstret för blobbar i sådana fall är PT1H_ #. JSON, där # är det stegvisa antalet blobar.

Data formatet lagrings konto är [JSON-linjer](../essentials/resource-logs-blob-format.md). Det innebär att varje post avgränsas med en ny rad, utan matris för yttre poster och inga kommatecken mellan JSON-poster. 

[![Exempel data för lagring](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Log Analytics data export kan skriva till att lägga till blobar till oföränderliga lagrings konton när tidsbaserade bevarande principer har inställningen *allowProtectedAppendWrites* aktive rad. Detta gör det möjligt att skriva nya block till en append-BLOB, samtidigt som oföränderlighets skydd och efterlevnad upprätthålls. Se [Tillåt skyddade bifogade BLOB-skrivningar](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

> [!NOTE]
> Att lägga till BLOB-stöd för Azure Data Lake Storage är nu tillgängligt i för hands versionen i alla Azure-regioner. [Registrera dig för den begränsade offentliga för hands versionen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pURDk2NjMzUTVEVzU5UU1XUlRXSTlHSlkxQS4u) innan du skapar en export regel som Azure Data Lake Storage. Exporten fungerar inte utan denna registrering.

### <a name="event-hub"></a>Händelsehubb
Data skickas till händelsehubben i nära real tid när den når Azure Monitor. En Event Hub skapas för varje datatyp som du exporterar med namnet *am –* följt av namnet på tabellen. Tabellen *SecurityEvent* skulle till exempel skickas till en Event Hub med namnet ' *am-SecurityEvent*'. Om du vill att exporterade data ska uppnå en viss händelsehubben, eller om du har en tabell med ett namn som överskrider tecken gränsen på 47, kan du ange ett eget namn på händelsehubben och exportera alla data för definierade tabeller till den.

> [!IMPORTANT]
> [Antalet Event Hub som stöds per namnrymd är 10](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers). Om du exporterar fler än 10 tabeller anger du ett eget namn på händelsehubben för att exportera alla tabeller till den händelsehubben.

Överväganden:
1. Den grundläggande Event Hub-SKU: n stöder lägre storleks [gräns](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-tiers) för händelser och vissa loggar på din arbets yta kan överstiga den och tas bort. Vi rekommenderar att du använder "standard" eller "dedikerad" händelsehubben som export mål.
2. Volymen för exporterade data ökar ofta med tiden och skalningen av Event Hub måste ökas för att hantera större överföringshastigheter och undvika begränsnings scenarier och data fördröjning. Du bör använda funktionen för automatisk ökning i Event Hubs för att automatiskt skala upp och öka antalet data flödes enheter och uppfylla användnings behoven. Mer information finns i [skala upp Azure Event Hubs data flödes enheter automatiskt](../../event-hubs/event-hubs-auto-inflate.md) .

## <a name="prerequisites"></a>Förutsättningar
Följande är förutsättningar som måste slutföras innan du konfigurerar Log Analytics data export.

- Lagrings kontot och händelsehubben måste redan skapas och måste finnas i samma region som Log Analytics-arbetsytan. Om du behöver replikera dina data till andra lagrings konton kan du använda något av [alternativen för Azure Storage redundans](../../storage/common/storage-redundancy.md).  
- Lagrings kontot måste vara StorageV1 eller StorageV2. Klassisk lagring stöds inte  
- Om du har konfigurerat ditt lagrings konto för att tillåta åtkomst från valda nätverk måste du lägga till ett undantag i inställningarna för ditt lagrings konto så att Azure Monitor kan skriva till lagringen.

## <a name="enable-data-export"></a>Aktivera data export
Följ stegen måste utföras för att aktivera Log Analytics data export. Se följande avsnitt för mer information om var och en.

- Registrera resurs leverantör.
- Tillåt betrodda Microsoft-tjänster.
- Skapa en eller flera data export regler som definierar de tabeller som ska exporteras och deras mål.

### <a name="register-resource-provider"></a>Registrera resursprovider
Följande Azure-adressresurs måste vara registrerad för din prenumeration för att aktivera Log Analytics data export. 

- Microsoft. Insights

Den här resurs leverantören är antagligen redan registrerad för de flesta Azure Monitor användare. För att verifiera går du till **prenumerationer** i Azure Portal. Välj din prenumeration och klicka sedan på **resurs leverantörer** i avsnittet **Inställningar** på menyn. Leta upp **Microsoft. Insights**. Om dess status är **registrerad** är den redan registrerad. Annars klickar du på **Registrera** för att registrera den.

Du kan också använda någon av de tillgängliga metoderna för att registrera en resurs leverantör enligt beskrivningen i [Azure Resource providers och-typer](../../azure-resource-manager/management/resource-providers-and-types.md). Följande är ett exempel kommando som använder PowerShell:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Tillåt betrodda Microsoft-tjänster
Om du har konfigurerat ditt lagrings konto för att tillåta åtkomst från valda nätverk måste du lägga till ett undantag för att tillåta Azure Monitor att skriva till kontot. Från **brand väggar och virtuella nätverk** för ditt lagrings konto väljer **du Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot**.

[![Lagrings kontots brand väggar och virtuella nätverk](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)

### <a name="create-or-update-data-export-rule"></a>Skapa eller uppdatera data export regel
En data export regel definierar de tabeller för vilka data exporteras och målet. Du kan skapa en enskild regel för varje mål för närvarande.

Export regeln ska innehålla tabeller som du har i din arbets yta. Kör den här frågan för en lista över tillgängliga tabeller i din arbets yta.

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Ej tillämpligt

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ej tillämpligt

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande kommando för att skapa en data export regel till ett lagrings konto med hjälp av CLI.

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

Använd följande kommando för att skapa en data export regel till en händelsehubben med hjälp av CLI. En separat händelsehubben skapas för varje tabell.

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

Använd följande kommando för att skapa en data export regel för en speciell händelsehubben med hjälp av CLI. Alla tabeller exporteras till det angivna händelsehubben. 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventHubName/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

Använd följande begäran för att skapa en data export regel med hjälp av REST API. Begäran bör använda token token-auktorisering och innehålls typ Application/JSON.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

Bröd texten i begäran anger mål för tabellen. Följande är en exempel text för REST-begäran för ett lagrings konto.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

Följande är en exempel text för REST-begäran för en Event Hub.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

Följande är en exempel text för REST-begäran för en Event Hub där Event Hub-namn anges. I det här fallet skickas alla exporterade data till den här händelsehubben.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

# <a name="template"></a>[Mall](#tab/json)

Använd följande kommando för att skapa en data export regel för ett lagrings konto med hjälp av mallen.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Använd följande kommando för att skapa en data export regel till en händelsehubben med hjälp av mall. En separat händelsehubben skapas för varje tabell.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Använd följande kommando för att skapa en data export regel för en speciell händelsehubben med hjälp av mallen. Alla tabeller exporteras till det angivna händelsehubben.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>Visa data export regel konfiguration

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Ej tillämpligt

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ej tillämpligt

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande kommando för att visa konfigurationen av en data export regel med hjälp av CLI.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Använd följande begäran om du vill visa konfigurationen av en data export regel med hjälp av REST API. Begäran bör använda token token-auktorisering.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Mall](#tab/json)

Ej tillämpligt

---

## <a name="disable-an-export-rule"></a>Inaktivera en export regel

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Ej tillämpligt

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ej tillämpligt

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Export regler kan inaktive ras så att du kan stoppa exporten när du inte behöver spara data under en viss period, till exempel när testningen utförs. Använd följande kommando för att inaktivera en data export regel med CLI.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

Export regler kan inaktive ras så att du kan stoppa exporten när du inte behöver spara data under en viss period, till exempel när testningen utförs. Använd följande begäran om du vill inaktivera en data export regel med hjälp av REST API. Begäran bör använda token token-auktorisering.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

# <a name="template"></a>[Mall](#tab/json)

Export regler kan inaktive ras så att du kan stoppa exporten när du inte behöver spara data under en viss period, till exempel när testningen utförs. Ange ```"enable": false``` i mall för att inaktivera en data export.

---

## <a name="delete-an-export-rule"></a>Ta bort en export regel

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Ej tillämpligt

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ej tillämpligt

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande kommando för att ta bort en data export regel med CLI.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Använd följande begäran om du vill ta bort en data export regel med hjälp av REST API. Begäran bör använda token token-auktorisering.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Mall](#tab/json)

Ej tillämpligt

---

## <a name="view-all-data-export-rules-in-a-workspace"></a>Visa alla data export regler i en arbets yta

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Ej tillämpligt

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ej tillämpligt

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande kommando för att visa alla data export regler i en arbets yta med CLI.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

Använd följande begäran om du vill visa alla data export regler i en arbets yta med hjälp av REST API. Begäran bör använda token token-auktorisering.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[Mall](#tab/json)

Ej tillämpligt

---

## <a name="unsupported-tables"></a>Tabeller som inte stöds
Om data export regeln innehåller en tabell som inte stöds kommer konfigurationen att lyckas, men inga data exporteras för tabellen. Om tabellen senare stöds, kommer dess data att exporteras vid denna tidpunkt.

Om data export regeln innehåller en tabell som inte finns kommer den inte att fungera med felet "tabellen <tableName> finns inte i arbets ytan".


## <a name="supported-tables"></a>Tabeller som stöds
Tabeller som stöds är för närvarande begränsade till dem som anges nedan. Alla data från tabellen exporteras om inte begränsningar anges. Den här listan kommer att uppdateras när stöd för ytterligare tabeller läggs till.


| Tabell | Begränsningar |
|:---|:---|
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADServicePrincipalSignInLogs |  |
| ABSBotRequests |  |
| ACSBillingUsage |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFTriggerRun |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| Varning |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | Delvis stöd – vissa data matas in via interna tjänster som inte stöds för export. Den här delen saknas i export för närvarande. |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DnsEvents |  |
| DnsInventory |  |
| Dynamics365Activity |  |
| Händelse | Delvis stöd – en del av data till den här tabellen matas in via lagrings kontot. Den här delen saknas i export för närvarande. |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| Pulsslag |  |
| HuntingBookmark |  |
| InsightsMetrics | Delvis stöd – vissa data matas in via interna tjänster som inte stöds för export. Den här delen saknas i export för närvarande. |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| OfficeActivity | Delvis stöd – några av de data som matas in via Webhooks från O365 till LA. Den här delen saknas i export för närvarande. |
| Åtgärd | Delvis stöd – vissa data matas in via interna tjänster som inte stöds för export. Den här delen saknas i export för närvarande. |
| Prest | Delvis stöd – endast Windows perf-data stöds för närvarande. Linux perf-data saknas i export för närvarande. |
| PowerBIDatasetsTenant |  |
| PowerBIDatasetsWorkspace |  |
| PowerBIDatasetsWorkspacePreview |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityDetection |  |
| SecurityEvent | Delvis stöd – en del av data till den här tabellen matas in via lagrings kontot. Den här delen saknas i export för närvarande. |
| Säkerhets incident |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| Syslog | Delvis stöd – en del av data till den här tabellen matas in via lagrings kontot. Den här delen saknas i export för närvarande. |
| ThreatIntelligenceIndicator |  |
| Uppdatera | Delvis stöd – vissa data matas in via interna tjänster som inte stöds för export. Den här delen saknas i export för närvarande. |
| UpdateRunProgress |  |
| UpdateSummary |  |
| Användning |  |
| Visnings lista |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | Delvis stöd – vissa data matas in via interna tjänster som inte stöds för export. Den här delen saknas i export för närvarande. |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |


## <a name="next-steps"></a>Nästa steg

- [Fråga exporterade data från Azure datautforskaren](../logs/azure-data-explorer-query-storage.md).
