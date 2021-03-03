---
title: Så här integrerar du det vanliga aviserings schemat med Logic Apps
description: Lär dig hur du skapar en logisk app som använder det gemensamma aviserings schemat för att hantera alla dina aviseringar.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 4824c5ab1826260ee1eb3639712d7138c7c85bfe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738675"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Så här integrerar du det vanliga aviserings schemat med Logic Apps

Den här artikeln visar hur du skapar en Logi Kap par som utnyttjar det gemensamma aviserings schemat för att hantera alla dina aviseringar.

## <a name="overview"></a>Översikt

Det [vanliga aviserings schemat](./alerts-common-schema.md) innehåller ett standardiserat och utöknings Bart JSON-schema över alla dina olika aviserings typer. Det vanliga aviserings schemat är mest användbart när du använder program mässigt – via Webhooks, Runbooks och Logic Apps. I den här artikeln visar vi hur du kan använda en enda Logic-app för att hantera alla aviseringar. Samma principer kan tillämpas på andra programmerings metoder. Logic-appen som beskrivs i den här artikeln skapar väldefinierade variabler för [fälten "viktigt"](alerts-common-schema-definitions.md#essentials)och beskriver hur du kan hantera en viss logik för [aviserings typen](alerts-common-schema-definitions.md#alert-context) .


## <a name="prerequisites"></a>Förutsättningar 

Den här artikeln förutsätter att läsaren är bekant med 
* Konfigurera aviserings regler ([mått](../alerts/alerts-metric.md), [logg](./alerts-log.md), [aktivitets logg](./alerts-activity-log.md))
* Konfigurera [Åtgärds grupper](./action-groups.md)
* Aktivera det [vanliga aviserings schemat](./alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) i åtgärds grupper

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Skapa en logisk app som utnyttjar det vanliga aviserings schemat

1. Följ [stegen som beskrivs för att skapa din Logic app](./action-groups-logic-app.md). 

1.  Välj utlösaren: **när en HTTP-begäran tas emot**.

    ![Utlösare för logikappar](media/action-groups-logic-app/logic-app-triggers.png "Utlösare för logikappar")

1.  Välj **Redigera** för att ändra utlösaren för http-begäran.

    ![Utlösare för HTTP-begäran](media/action-groups-logic-app/http-request-trigger-shape.png "Utlösare för HTTP-begäran")


1.  Kopiera och klistra in följande schema:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Välj **+** **nytt steg** och välj sedan **Lägg till en åtgärd**.

    ![Lägga till en åtgärd](media/action-groups-logic-app/add-action.png "Lägga till en åtgärd")

1. I det här skedet kan du lägga till en mängd anslutningar (Microsoft Teams, slack, Salesforce osv.) utifrån dina specifika affärs behov. Du kan använda den "nödvändiga fälten" i-rutan. 

    ![Viktiga fält](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Viktiga fält")
    
    Alternativt kan du redigera villkorlig logik baserat på aviserings typen med hjälp av alternativet "uttryck".

    ![Logic app-uttryck](media/alerts-common-schema-integrations/logic-app-expressions.png "Logic app-uttryck")
    
     Med [fältet monitoringService](alerts-common-schema-definitions.md#alert-context) kan du unikt identifiera aviserings typen, baserat på vilken du kan skapa den villkorliga logiken.

    
    Till exempel kontrollerar kodfragmentet nedan om aviseringen är en Application Insights baserad logg avisering, och om så skrivs ut Sök resultaten. Annars skrivs "NA" ut.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Lär dig mer om att [skriva Logic app-uttryck](../../logic-apps/workflow-definition-language-functions-reference.md#logical-comparison-functions).

    


## <a name="next-steps"></a>Nästa steg

* [Läs mer om åtgärds grupper](./action-groups.md).
* [Läs mer om det vanliga aviserings schemat](./alerts-common-schema.md).