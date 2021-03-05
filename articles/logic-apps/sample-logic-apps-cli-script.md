---
title: Skript exempel för Azure CLI – skapa en logisk app
description: Exempel skript för att skapa en Logic-app via Logic Apps-tillägget i Azure CLI.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: a4553ceee482fb232e9ab56deca650be93f9dc6b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218051"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Skript exempel för Azure CLI – skapa en logisk app

Det här skriptet skapar en exempel-Logic-app via [Azure CLI Logic Apps-tillägget](/cli/azure/ext/logic/logic)( `az logic` ). En detaljerad guide för att skapa och hantera Logi Kap par via Azure CLI finns i [Logic Apps snabb start för Azure CLI](quickstart-logic-apps-azure-cli.md).

> [!WARNING]
> Azure CLI Logic Apps-tillägget är för närvarande *experimentellt* och *omfattas inte av kund support*. Använd det här CLI-tillägget med försiktighet, särskilt om du väljer att använda tillägget i produktions miljöer.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Azure CLI](/cli/azure/install-azure-cli) installerat på den lokala datorn.
* [Logic Apps Azure CLI-tillägget](/cli/azure/azure-cli-extensions-list) installerat på datorn. Använd följande kommando för att installera det här tillägget: `az extension add --name logic`
* En [arbets flödes definition](quickstart-logic-apps-azure-cli.md#workflow-definition) för din Logic app. Den här JSON-filen måste följa [språk schemat för arbets flödes definitionen](logic-apps-workflow-definition-language.md).
* En API-anslutning till ett e-postkonto via en [Logic Apps](../connectors/apis-list.md) -anslutning som stöds i samma resurs grupp som din Logic app. I det här exemplet används [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) Connector, men du kan också använda andra anslutningar som [Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Krav kontroll

Verifiera din miljö innan du börjar:

* Logga in på Azure Portal och kontrol lera att din prenumeration är aktiv genom att köra `az login` .

* Kontrol lera din version av Azure CLI i ett terminalfönster eller kommando fönster genom att köra `az --version` . Den senaste versionen finns i den senaste versions [informationen](/cli/azure/release-notes-azure-cli).

  * Om du inte har den senaste versionen uppdaterar du installationen genom att följa [installations guiden för ditt operativ system eller din plattform](/cli/azure/install-azure-cli).

### <a name="sample-workflow-explanation"></a>Exempel på arbets flödes förklaring

Den här exempel filen för arbets flödes definition skapar samma grundläggande Logic-app som [Logic Apps snabb start för Azure Portal](quickstart-create-first-logic-app-workflow.md). 

Följande exempel arbets flöde: 

1. Anger ett schema, `$schema` för Logic app.

1. Definierar en utlösare för Logic-appen i listan över utlösare `triggers` . Utlösaren upprepas ( `recurrence` ) var 3: e timme. Åtgärderna utlöses när ett nytt flödes objekt publiceras ( `When_a_feed_item_is_published` ) för det angivna RSS-flödet ( `feedUrl` ).

1. Definierar en åtgärd för Logic-appen i listan över åtgärder `actions` . Åtgärden skickar ett e-postmeddelande ( `Send_an_email_(V2)` ) via Microsoft 365 med information från RSS-feed-objekten som anges i avsnittet Body ( `body` ) i åtgärdens indata ( `inputs` ).

## <a name="sample-workflow-definition"></a>Exempel på arbets flödes definition

Innan du kör exempel skriptet måste du först skapa en exempel [arbets flödes definition](#prerequisites).

1. Skapa en JSON-fil `testDefinition.json` på din dator. 

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

1. Uppdatera plats hållarnas värden med din egen information:

    1. Ersätt e-postadressen för plats hållaren ( `"To": "test@example.com"` ). Du måste använda en e-postadress som är kompatibel med Logic Apps-kopplingar. Mer information finns i [krav](#prerequisites).

    1. Ersätt ytterligare anslutnings information om du använder en annan e-postkoppling än Office 365 Outlook Connector.

    1. Ersätt plats hållarnas prenumerations värden ( `00000000-0000-0000-0000-000000000000` ) för anslutnings-ID: n ( `connectionId` och `id` ) under anslutnings parametern ( `$connections` ) med dina egna prenumerations värden.

1. Spara ändringarna.

## <a name="sample-script"></a>Exempelskript

> [!NOTE]
> Det här exemplet är skrivet för `bash` gränssnittet. Om du vill köra det här exemplet i ett annat gränssnitt, t. ex. Windows PowerShell eller kommando tolken, kan du behöva göra ändringar i skriptet.

Innan du kör det här exempel skriptet ska du köra det här kommandot för att ansluta till Azure:

```azurecli-interactive

az login

```

Gå sedan till den katalog där du skapade arbets flödes definitionen. Om du till exempel har skapat JSON-filen för arbets flödes definition på Skriv bordet:

```azurecli

cd ~/Desktop

```

Kör sedan det här skriptet för att skapa en Logic app. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Rensa distribution

När du är klar med exempel skriptet kör du följande kommando för att ta bort resurs gruppen och alla dess kapslade resurser, inklusive Logic app.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Förklaring av skript

Det här exempel skriptet använder följande kommandon för att skapa en ny resurs grupp och en logisk app.

| Kommando | Kommentarer |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az-group-create) | Skapar en resurs grupp där din Logic Apps-resurser lagras. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) | Skapar en logisk app baserat på det arbets flöde som definierats i parametern `--definition` . |
| [`az group delete`](/cli/azure/vm/extension) | Tar bort en resurs grupp och alla dess kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure/).

Du hittar ytterligare Logic Apps CLI-skript exempel i [Microsofts webbläsare för kod exempel](/samples/browse/?products=azure-logic-apps).
