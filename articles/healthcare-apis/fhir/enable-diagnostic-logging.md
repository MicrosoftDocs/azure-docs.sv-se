---
title: Aktivera diagnostisk loggning i Azure API för FHIR
description: I den här artikeln förklaras hur du aktiverar diagnostisk loggning i Azure API för FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 03/03/2021
ms.openlocfilehash: d8f7a2b2f31fb192147c1950866cff77064a3671
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020320"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Aktivera diagnostisk loggning i Azure API för FHIR

I den här artikeln får du lära dig hur du aktiverar diagnostisk loggning i Azure API för FHIR och kan granska några exempel frågor för dessa loggar. Åtkomst till diagnostikloggar är nödvändig för alla sjukvårds tjänster där efterlevnaden av myndighets krav (t. ex. HIPAA) är en måste. Funktionen i Azure API för FHIR som möjliggör diagnostikloggar är [**diagnostikinställningar**](../../azure-monitor/essentials/diagnostic-settings.md) i Azure Portal. 

## <a name="view-and-download-fhir-metrics-data"></a>Visa och ladda ned FHIR Metrics-data

Du kan visa måtten under övervakning | Mått från portalen. Måtten omfattar antalet begär Anden, genomsnittlig svars tid, antal fel, data storlek, ru: er som används, antalet begär Anden som överskrider kapacitet och tillgänglighet (i%). Skärm bilden nedan visar ru: er som används för en exempel miljö med mycket få aktiviteter under de senaste 7 dagarna. Du kan hämta data i JSON-format.

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="Azure API för FHIR-mått från portalen" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>Aktivera gransknings loggar
1. Om du vill aktivera diagnostisk loggning i Azure API för FHIR, väljer du din Azure API för FHIR-tjänst i Azure Portal 
2. Navigera till **diagnostikinställningar** 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="Lägg till inställningar för Azure FHIR-diagnostik." lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. Välj **+ Lägg till diagnostisk inställning**

4. Ange ett namn för inställningen

5. Välj den metod som du vill använda för att få åtkomst till dina diagnostikloggar:

    1. **Arkivera till ett lagrings konto** för granskning eller manuell kontroll. Det lagrings konto som du vill använda måste redan ha skapats.
    2. **Strömma till händelsehubben** för inmatning av en tjänst från tredje part eller en anpassad analys lösning. Du måste skapa ett namn område och en Event Hub-princip innan du kan konfigurera det här steget.
    3. **Strömma till Log Analytics** arbets ytan i Azure Monitor. Du måste skapa din loggar Analytics-arbetsyta innan du kan välja det här alternativet.

6. Välj **AuditLogs** och/eller **AllMetrics**. Måtten är bland annat tjänst namn, tillgänglighet, data storlek, total svars tid, totalt antal fel och tidsstämpel. Du hittar mer information om [vilka mått som stöds](https://docs.microsoft.com/azure/azure-monitor/essentials/metrics-supported#microsofthealthcareapisservices). 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Diagnostiska inställningar för Azure FHIR. Välj AuditLogs och/eller AllMetrics." lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. Välj **Spara**


> [!Note] 
> Det kan ta upp till 15 minuter innan de första loggarna visas i Log Analytics. Om Azure API för FHIR flyttas från en resurs grupp eller prenumeration till en annan, uppdaterar du inställningen när flyttningen är klar. 
 
Mer information om hur du arbetar med diagnostikloggar finns i [dokumentationen till Azure Resource log](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="audit-log-details"></a>Gransknings logg information
För närvarande returnerar Azure API för FHIR-tjänsten följande fält i gransknings loggen: 

|Fältnamn  |Typ  |Kommentarer  |
|---------|---------|---------|
|CallerIdentity|Dynamisk|En generisk egenskaps uppsättning med identitets information
|CallerIdentityIssuer|Sträng|Utfärdare 
|CallerIdentityObjectId|Sträng|Object_Id 
|CallerIPAddress|Sträng|Anroparens IP-adress 
|CorrelationId|Sträng| Korrelations-ID
|FhirResourceType|Sträng|Resurs typen som åtgärden kördes för
|LogCategory|Sträng|Logg kategorin (vi returnerar AuditLogs LogCategory)
|Location|Sträng|Platsen för den server som bearbetade begäran (t. ex. södra centrala USA)
|OperationDuration|Int|Tiden det tog att slutföra den här begäran på några sekunder
|OperationName|Sträng| Beskriver typ av åtgärd (t. ex. uppdatering, söknings typ)
|RequestUri|Sträng|Begärd URI 
|ResultType|Sträng|De tillgängliga värdena är för närvarande **igång**, **lyckades** eller **misslyckades**
|StatusCode|Int|HTTP-statuskod. (t. ex. 200) 
|TimeGenerated|DateTime|Datum och tid för händelsen|
|Egenskaper|Sträng| Beskriver egenskaperna för fhirResourceType
|SourceSystem|Sträng| Käll system (alltid i Azure i detta fall)
|TenantId|Sträng|Klient-ID:t
|Typ|Sträng|Typ av logg (alltid MicrosoftHealthcareApisAuditLog i det här fallet)
|_ResourceId|Sträng|Information om resursen

## <a name="sample-queries"></a>Exempelfrågor

Här följer några grundläggande Application Insights frågor som du kan använda för att utforska dina loggdata.

Kör den här frågan för att se de **100 senaste** loggarna:

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Kör den här frågan för att gruppera åtgärder efter **FHIR resurs typ**:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

Kör den här frågan för att hämta alla **misslyckade resultat**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Slutsats 
Att ha åtkomst till diagnostikloggar är viktigt för att övervaka en tjänst och tillhandahålla rapporter om efterlevnad. Med Azure API för FHIR kan du utföra dessa åtgärder via diagnostikloggar. 
 
FHIR är ett registrerat varumärke som tillhör HL7 och används med tillåtelse av HL7.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du aktiverar gransknings loggar för Azure API för FHIR. Läs sedan mer om andra ytterligare inställningar som du kan konfigurera i Azure API för FHIR
 
>[!div class="nextstepaction"]
>[Ytterligare inställningar](azure-api-for-fhir-additional-settings.md)
