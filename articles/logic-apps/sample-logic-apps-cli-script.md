---
title: Skriptexempel för Azure CLI – skapa en logikapp
description: Exempelskript för att skapa en logikapp via Logic Apps i Azure CLI.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: 8086ce0e5964d4e37a5ffc3082d5f2856058e4e5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875493"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Skriptexempel för Azure CLI – skapa en logikapp

Det här skriptet skapar en exempellogikapp via [Azure CLI Logic Apps tillägget](/cli/azure/logic), ( `az logic` ). En detaljerad guide för att skapa och hantera logikappar via Azure CLI finns i [Logic Apps snabbstart för Azure CLI.](quickstart-logic-apps-azure-cli.md)

> [!WARNING]
> Azure CLI Logic Apps tillägget är för närvarande *experimentellt* *och omfattas inte av kundsupporten.* Använd det här CLI-tillägget med försiktighet, särskilt om du väljer att använda tillägget i produktionsmiljöer.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Azure CLI installerat](/cli/azure/install-azure-cli) på den lokala datorn.
* Den [Logic Apps Azure CLI-tillägget](/cli/azure/azure-cli-extensions-list) som är installerat på datorn. Om du vill installera det här tillägget använder du det här kommandot: `az extension add --name logic`
* En [arbetsflödesdefinition](quickstart-logic-apps-azure-cli.md#workflow-definition) för logikappen. Den här JSON-filen måste följa [språkschemat arbetsflödesdefinition.](logic-apps-workflow-definition-language.md)
* En API-anslutning till ett e-postkonto via [en Logic Apps i](../connectors/apis-list.md) samma resursgrupp som logikappen. I det här exemplet [används Office 365 Outlook-anslutningsappen,](../connectors/connectors-create-api-office365-outlook.md) men du kan också använda andra anslutningsappar [som Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Kravkontroll

Verifiera din miljö innan du börjar:

* Logga in på Azure Portal och kontrollera att din prenumeration är aktiv genom att köra `az login` .

* Kontrollera din version av Azure CLI i en terminal eller ett kommandofönster genom att köra `az --version` . Den senaste versionen finns i den senaste [versionsanteckningarna.](/cli/azure/release-notes-azure-cli)

  * Om du inte har den senaste versionen uppdaterar du installationen genom att följa [installationsguiden för ditt operativsystem eller din plattform.](/cli/azure/install-azure-cli)

### <a name="sample-workflow-explanation"></a>Förklaring av exempelarbetsflöde

Den här exempelarbetsflödesdefinitionsfilen skapar samma grundläggande logikapp [som Logic Apps snabbstarten för Azure Portal](quickstart-create-first-logic-app-workflow.md). 

Det här exempelarbetsflödet: 

1. Anger ett schema, `$schema` , för logikappen.

1. Definierar en utlösare för logikappen i listan över utlösare, `triggers` . Utlösaren upprepas ( `recurrence` ) var tredje timme. Åtgärderna utlöses när ett nytt flödesobjekt publiceras ( `When_a_feed_item_is_published` ) för det angivna RSS-flödet ( `feedUrl` ).

1. Definierar en åtgärd för logikappen i listan över åtgärder, `actions` . Åtgärden skickar ett e-postmeddelande ( ) via Microsoft 365 med information från RSS-flödesobjekten som anges i brödtextavsnittet ( ) för `Send_an_email_(V2)` `body` åtgärdens indata ( `inputs` ).

## <a name="sample-workflow-definition"></a>Exempel på arbetsflödesdefinition

Innan du kör exempelskriptet måste du först skapa en [exempelarbetsflödesdefinition](#prerequisites).

1. Skapa en JSON-fil `testDefinition.json` på datorn. 

1. Kopiera följande innehåll till JSON-filen: 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. Uppdatera platshållarvärdena med din egen information:

    1. Ersätt platshållarens e-postadress ( `"To": "test@example.com"` ). Du måste använda en e-postadress som är kompatibel Logic Apps anslutningsappar. Mer information finns i [förutsättningarna](#prerequisites).

    1. Ersätt ytterligare information om anslutningsappen om du använder en annan e-postanslutningsapp än Office 365 Outlook-anslutningsappen.

    1. Ersätt platshållarprenumerationens värden ( ) för anslutningsidentifierarna ( och `00000000-0000-0000-0000-000000000000` `connectionId` ) under `id` anslutningsparametern ( `$connections` ) med dina egna prenumerationsvärden.

1. Spara ändringarna.

## <a name="sample-script"></a>Exempelskript

> [!NOTE]
> Det här exemplet är skrivet för `bash` gränssnittet. Om du vill köra det här exemplet i ett annat gränssnitt, till exempel Windows PowerShell eller kommandotolk, kan du behöva göra ändringar i skriptet.

Innan du kör det här exempelskriptet kör du det här kommandot för att ansluta till Azure:

```azurecli-interactive

az login

```

Gå sedan till katalogen där du skapade arbetsflödesdefinitionen. Om du till exempel har skapat JSON-filen med arbetsflödesdefinitionen på skrivbordet:

```azurecli

cd ~/Desktop

```

Kör sedan skriptet för att skapa en logikapp. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Rensa distribution

När du har använt exempelskriptet kör du följande kommando för att ta bort resursgruppen och alla dess kapslade resurser, inklusive logikappen.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Förklaring av skript

Det här exempelskriptet använder följande kommandon för att skapa en ny resursgrupp och logikapp.

| Kommando | Kommentarer |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az_group_create) | Skapar en resursgrupp där logikappens resurser lagras. |
| [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) | Skapar en logikapp baserat på arbetsflödet som definierats i parametern `--definition` . |
| [`az group delete`](/cli/azure/vm/extension) | Tar bort en resursgrupp och alla dess kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen.](/cli/azure/)

Du hittar ytterligare Logic Apps CLI-skriptexempel [i Microsofts webbläsare för kodexempel.](/samples/browse/?products=azure-logic-apps)
