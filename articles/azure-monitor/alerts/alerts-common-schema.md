---
title: Vanligt aviserings schema för Azure Monitor aviseringar
description: Förstå det vanliga aviserings schemat, varför du bör använda det och hur du aktiverar det
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 39ea469b4109f4715db0d0a73ad069121058a46f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038265"
---
# <a name="common-alert-schema"></a>Vanligt aviseringsschema

I den här artikeln beskrivs det vanliga aviserings schemat, fördelarna med att använda det och hur du aktiverar det.

## <a name="what-is-the-common-alert-schema"></a>Vad är det vanliga aviserings schemat?

Det vanliga aviserings schemat standardiserar användnings upplevelsen för aviserings meddelanden i Azure idag. Tidigare var de tre aviserings typerna i Azure idag (Metric, loggen och aktivitets loggen) haft egna e-postmallar, webhook-scheman osv. Med det gemensamma aviserings schemat kan du nu få aviserings meddelanden med ett konsekvent schema.

Alla varnings instanser beskriver **den resurs som påverkades** och **orsaken till aviseringen**, och dessa instanser beskrivs i det gemensamma schemat i följande avsnitt:
* **Essentials**: en uppsättning **standardiserade fält**, gemensamma för alla aviserings typer, som beskriver **vilken resurs** som aviseringen är på samt ytterligare vanliga aviserings-metadata (till exempel allvarlighets grad eller beskrivning). 
* **Aviserings kontext**: en uppsättning fält som beskriver **orsaken till aviseringen**, med fält som varierar **beroende på aviserings typen**. Till exempel skulle en mått avisering ha fält som Metric-namn och mått värde i aviserings kontexten, medan en aktivitets logg avisering skulle ha information om händelsen som genererade aviseringen. 

De typiska integrerings scenarierna som vi hör från kunder innebär att aviserings instansen vidarebefordras till den berörda gruppen baserat på en pivot (t. ex. resurs grupp), efter vilken det ansvariga teamet börjar arbeta på den. Med det gemensamma aviserings schemat kan du ha standardiserad cirkulations logik över aviserings typer genom att använda de viktigaste fälten, lämna kontext fälten som det är för de berörda teamen att undersöka vidare.

Det innebär att du kan ha färre integrationer, vilket gör att du kan hantera och underhålla dem en _mycket_ enklare uppgift. Framtida nytto last för aviserings nytto laster (till exempel anpassning, Diagnostic-anrikning osv.) kommer också att placeras i det gemensamma schemat.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Vilka förbättringar tar det vanliga aviserings schemat?

Det vanliga aviserings schemat kommer huvudsakligen att manifesta sig själva i aviserings aviseringarna. De förbättringar som du ser visas nedan:

| Action | Förbättringar |
|:---|:---|
| SMS | En konsekvent SMS-mall för alla aviserings typer. |
| E-post | En konsekvent och detaljerad e-postmall som gör det enkelt att snabbt diagnostisera problem. Inbäddade djup länkar till aviserings instansen på portalen och den berörda resursen garanterar att du snabbt kan gå vidare till reparations processen. |
| Webhook/Logic app/Azure Function/Automation Runbook | En konsekvent JSON-struktur för alla aviserings typer, vilket gör att du enkelt kan bygga integreringar över olika aviserings typer. |

Det nya schemat kommer också att aktivera en rikare aviserings användning i både Azure Portal och Azure-mobilapp i omedelbar framtid. 

[Lär dig mer om schema definitionerna för Webhooks/Logic Apps/Azure Functions/Automation-runbooks.](./alerts-common-schema-definitions.md)

> [!NOTE]
> Följande åtgärder stöder inte det gemensamma aviserings schemat: ITSM-anslutningsprogram.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Vill du Hur gör jag för att aktivera det vanliga aviserings schemat?

Du kan välja att välja ett gemensamt aviserings schema via åtgärds grupper på både portalen och i REST API. Växlingen för att växla till det nya schemat finns på en åtgärds nivå. Du måste till exempel separat välja för en e-poståtgärd och en webhook-åtgärd.

> [!NOTE]
> 1. Följande aviserings typer stöder gemensamt schema som standard (inte opt-in krävs):
>     * Aviseringar för smart identifiering
> 1. Följande aviserings typer stöder för närvarande inte det gemensamma schemat:
>     * Aviseringar som genererats av [VM Insights](../vm/vminsights-overview.md)
>     * Aviseringar som genererats av [Azure Cost Management](../../cost-management-billing/manage/cost-management-budget-scenario.md)

### <a name="through-the-azure-portal"></a>Via Azure Portal

![Eget deltagande i aviserings schema](media/alerts-common-schema/portal-opt-in.png)

1. Öppna en befintlig eller ny åtgärd i en åtgärds grupp. 
1. Välj Ja om du vill aktivera det gemensamma aviserings schemat enligt visning.

### <a name="through-the-action-groups-rest-api"></a>Genom REST API åtgärds grupper

Du kan också använda [Åtgärds grupps-API: et](/rest/api/monitor/actiongroups) för att välja gemensamt aviserings schema. När du gör ett anrop för att [skapa eller uppdatera](/rest/api/monitor/actiongroups/createorupdate) REST API kan du ange flaggan "useCommonAlertSchema" till "true" (för att välja) eller "falskt" (för att avanmäla) för någon av följande åtgärder – e-post/webhook/Logic app/Azure Function/Automation Runbook.

Exempel: följande begär ande text till [skapa-eller uppdaterings](/rest/api/monitor/actiongroups/createorupdate) REST API gör följande:

* Aktivera det vanliga aviserings schemat för e-poståtgärden John berg e-post
* Inaktivera det vanliga aviserings schemat för e-poståtgärden "Jane Smith" e-post "
* Aktivera det vanliga aviserings schemat för webhook-åtgärden "exempel-webhook"

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Nästa steg

- [Vanliga aviserings schema definitioner för Webhooks/Logic Apps/Azure Functions/Automation-runbooks.](./alerts-common-schema-definitions.md)
- [Lär dig hur du skapar en logisk app som använder det gemensamma aviserings schemat för att hantera alla dina aviseringar.](./alerts-common-schema-integrations.md)