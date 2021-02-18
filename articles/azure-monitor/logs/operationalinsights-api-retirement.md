---
title: Azure Monitor-API-pensionering
description: Beskriver indragningen av äldre versioner av OperationalInsights Resource Provider API.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: 6564e7263639f0a78df6f2674ce7a4b610fb0fc1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623746"
---
# <a name="operationalinsights-api-version-retirement"></a>Borttagning av OperationalInsights-API-version
Microsoft tillhandahåller ett meddelande minst 12 månader i förväg för att dra tillbaka ett API för att utjämna över gången till en nyare/version som stöds. Vi har släppt en ny version (2020-08-01) för **OperationalInsights** Resource Provider-API: er och kommer att dra tillbaka alla tidigare API-versioner den 29 februari 2024.

Vi rekommenderar att du börjar använda version 2020-08-01 nu för att få fördelarna med nya funktioner, till exempel [dedikerat kluster](../log-query/logs-dedicated-clusters.md), [Kundhanterade nycklar](../logs/customer-managed-keys.md), [privat länk](./private-link-security.md) och [data export](./logs-data-export.md). Nya funktioner och funktioner och optimeringar läggs också till i det aktuella API: t.

Efter den 29 februari 2024 kommer Azure Monitor inte längre att ha stöd för tidigare API-versioner än 2020-08-01. Om du föredrar att inte uppgradera fortsätter de begär Anden som skickas från tidigare versioner att betjänas av Azure Monitor tjänsten fram till den 29 februari 2024.

## <a name="migration-steps"></a>Migreringsanvisningar
Beroende på vilken konfigurations metod du använder bör du uppdatera den nya versionen i **rest** -begäranden och **Resource Manager-mallar**. Uppdatera API-versionen genom att följa exemplen nedan:

1. REST API begär Anden använder API-versionen i URL: en för begäran. Ersätt den versionen med den senaste versionen (2020-08-01) som du ser i följande exempel.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager mallar använder API-versionen i egenskapen **API version** för resursen. Ersätt den versionen med den senaste versionen (2020-08-01) som du ser i följande exempel.


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


### <a name="more-information"></a>Mer information
Om du har frågor kan du få svar från [våra experter på Tech-gruppen]( https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor). Om du har en Support plan och behöver teknisk hjälp skapar du en [supportbegäran]( https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest): 
1.  Välj **Technical** under *typ av problem*. 
2.  Välj din prenumeration under *Prenumeration*. 
3.  Under *tjänst* väljer du **Mina tjänster** och väljer sedan **Log Analytics**. 
4.  Under *Sammanfattning* anger du en beskrivning av problemet. 
5.  Under *problem typ* väljer du **Log Analytics arbets ytans hantering**.  
6.  Under *typ av problem* väljer du **arm-mallar, PowerShell och CLI**. 

## <a name="next-steps"></a>Nästa steg

- Se [referensen för OperationalInsights-arbetsyte-API: et](/rest/api/loganalytics/workspaces).