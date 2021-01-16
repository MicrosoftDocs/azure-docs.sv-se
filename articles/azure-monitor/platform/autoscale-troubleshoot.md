---
title: Felsöka Azures autoskalning
description: Spåra problem med automatisk skalning i Azure som används i Service Fabric, Virtual Machines, Web Apps och moln tjänster.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 8c4589acd17e76d1341d5aceada67e565c8f8c37
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251275"
---
# <a name="troubleshooting-azure-autoscale"></a>Felsöka Azures autoskalning
 
Azure Monitor autoskalning hjälper dig att få rätt mängd resurser som körs för att hantera belastningen på ditt program. Det gör att du kan lägga till resurser för att hantera ökad belastning och även spara pengar genom att ta bort resurser som är inaktiva. Du kan skala baserat på ett schema, fast datum/tid eller resurs mått som du väljer. Mer information finns i [Översikt över autoskalning](autoscale-overview.md).

Med tjänsten för autoskalning får du mått och loggar för att förstå vilka skalnings åtgärder som har inträffat och utvärderingen av de villkor som ledde till dessa åtgärder. Du kan hitta svar på frågor som:

- Varför är min tjänst skalbar eller i?
- Varför är min tjänst inte skalad?
- Varför misslyckades en autoskalning-åtgärd?
- Varför tar det tid att skala ut en åtgärd?
  
## <a name="autoscale-metrics"></a>Autoskalning av mått

Med autoskalning får du [fyra mått](metrics-supported.md#microsoftinsightsautoscalesettings) för att förstå driften. 

- **Observerat mått värde** – värdet för det mått som du valde att vidta skalnings åtgärden på, som det visas eller beräknas av den automatiska skalnings motorn. Eftersom en enskild inställning för autoskalning kan ha flera regler och därför flera metriska källor, kan du filtrera med "mått källa" som en dimension.
- **Mått tröskelvärde** – det tröskelvärde du ställer in för att utföra skalnings åtgärden. Eftersom en enskild inställning för autoskalning kan ha flera regler och därför flera metriska källor kan du filtrera med "mått regel" som en dimension.
- **Observerad kapacitet** – det aktiva antalet instanser av mål resursen som visas av motorn för autoskalning.
- **Skalningsåtgärder har initierats** – Antalet åtgärder för att skala ut och skala in som har initierats av autoskalningsmotorn. Du kan filtrera genom att skala ut eller skala ut i åtgärder.

Du kan använda [Metrics Explorer](metrics-getting-started.md) för att rita ut ovanstående mått på samma ställe. Diagrammet ska visa:

  - faktiskt mått
  - måttet som läst/beräknad av autoskalning-motorn
  - tröskeln för en skalnings åtgärd
  - förändringen i kapaciteten 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Exempel 1 – analysera en enkel regel för autoskalning 

Vi har en enkel autoskalning-inställning för en skalnings uppsättning för virtuella datorer som:

- skalar ut när den genomsnittliga CPU-procenten för en mängd är större än 70% i 10 minuter 
- skalar i när PROCESSORns procent andel är mindre än 5% i mer än 10 minuter. 

Vi går igenom måtten från AutoScale-tjänsten.
 
![Skärm bild som visar ett procent processor exempel för skalnings uppsättningar för virtuella datorer.](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Procent processor exempel för skalnings uppsättning för virtuell dator](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

**_Bild 1a – procent andel CPU-mått för skalnings uppsättningen för den virtuella datorn och det observerade mått svärdet mått för autoskalning Setting_* _

![Mätnings tröskel och observerad kapacitet](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

_*_Figur 1b – mätnings tröskel och observerad kapacitet_*_

I bild 1b är 70 *Metric-tröskelvärdet** (ljusblå blå linje) för den skalbara regeln.  Den **observerade kapaciteten** (mörk blå linje) visar antalet aktiva instanser, som för närvarande är 3. 

> [!NOTE]
> Du måste filtrera **mått tröskelvärdet** efter regel utlösare regel för regel utskalning (öka) för att se tröskelvärdet för skala ut och av skalan i regeln (minska). 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Exempel 2 – avancerad autoskalning för en skalnings uppsättning för virtuella datorer

Vi har en inställning för autoskalning som gör att en resurs för skalnings uppsättningar för virtuella datorer kan skalas ut baserat på sina egna mått **utgående flöden**. Observera att alternativet **dividera mått per instans antal** för mått tröskelvärdet är markerat. 

Skalnings åtgärds regeln är: 

Om värdet för **utgående flöde per instans** är större än 10 bör AutoScale-tjänsten skalas ut med en instans. 

I det här fallet beräknas det observerade mått svärdet för autoskalning som det faktiska mått svärdet dividerat med antalet instanser. Om det observerade Metric-värdet är mindre än tröskelvärdet initieras ingen skalnings åtgärd. 
 
![Skärm bild som visar sidan Genomsnittligt antal utgående flöden med ett exempel på mått diagram för skalnings uppsättning för virtuella datorer.](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Exempel på skalnings uppsättning för skalnings uppsättning för virtuell dator](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

**_Bild 2 – skal uppsättning för skalnings uppsättning för virtuella datorer, exempel_* _

I bild 2 kan du se två mått diagram. 

Diagrammet överst visar det faktiska värdet för _ *utgående flöden** mått. Det faktiska värdet är 6. 

Diagrammet längst ned visar några värden. 
 - Det **observerade mått svärdet** (ljus blått) är 3 eftersom det finns 2 aktiva instanser och 6 dividerat med 2 är 3. 
 - Den **observerade kapaciteten** (lila) visar antalet instanser som visas för autoskalning-motorn. 
 - **Mått tröskelvärdet** (ljus grönt) är inställt på 10. 

Om det finns flera skalnings åtgärds regler kan du använda dela upp eller **Lägg till filter** i diagrammet Metrics Explorer för att titta på mått med en bestämd källa eller regel. Mer information om hur du delar ett mått diagram finns i [avancerade funktioner i mått diagram – dela upp](metrics-charts.md#apply-splitting)

## <a name="example-3---understanding-autoscale-events"></a>Exempel 3 – förstå autoskalning-händelser

På skärmen Inställningar för autoskalning går du till fliken **körnings historik** för att se de senaste skalnings åtgärderna. Fliken visar även ändringen av den **observerade kapaciteten** över tid. Om du vill ha mer information om alla åtgärder för autoskalning, inklusive åtgärder som uppdatera/ta bort inställningar för autoskalning, kan du Visa aktivitets loggen och filtrera efter automatiska skalnings åtgärder.

![Kör historik för inställningar för autoskalning](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Autoskala resurs loggar

På samma sätt som andra Azure-resurser innehåller AutoScale-tjänsten [resurs loggar](platform-logs-overview.md). Det finns två typer av loggar.

- **Autoskalning-utvärderingar** – den automatiska skalnings motorn registrerar logg poster för varje enskild villkors utvärdering varje gång en kontroll utförs.  Posten innehåller information om de observerade värdena för måtten, reglerna utvärderas och om utvärderingen resulterade i en skalnings åtgärd eller inte.

- **Åtgärder för automatisk skalnings skalning** – motorn registrerar skalnings åtgärds händelser som initieras av tjänsten för automatisk skalning och resultatet av dessa skalnings åtgärder (lyckade, misslyckade och hur mycket skalning som har gjorts enligt autoskalning tjänsten).

Precis som med alla Azure Monitor tjänster som stöds kan du använda [diagnostikinställningar](diagnostic-settings.md) för att dirigera dessa loggar:

- till din Log Analytics arbets yta för detaljerad analys
- för att Event Hubs och sedan till icke-Azure-verktyg
- till ditt Azure Storage-konto för arkivering  

![Inställningar för autoskalning av diagnostik](media/autoscale-troubleshoot/diagnostic-settings.png)

Föregående bild visar Azure Portal inställningarna för autoskalning av diagnostik. Där kan du välja fliken diagnostik/resurs loggar och aktivera logg insamling och routning. Du kan också utföra samma åtgärd med REST API, CLI, PowerShell, Resource Manager-mallar för diagnostikinställningar genom att välja resurs typ som *Microsoft. Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Felsöka med hjälp av automatiska skalnings loggar 

För bästa fel söknings upplevelse rekommenderar vi att du dirigerar loggarna till Azure Monitor loggar (Log Analytics) via en arbets yta när du skapar inställningen för autoskalning. Den här processen visas i bilden i föregående avsnitt. Du kan validera utvärderingarna och skala åtgärder bättre med Log Analytics.

När du har konfigurerat dina autoskalning-loggar som ska skickas till Log Analytics arbets ytan kan du köra följande frågor för att kontrol lera loggarna. 

Kom igång genom att testa den här frågan för att visa de senaste utvärderings loggarna för autoskalning:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Eller prova följande fråga för att visa de senaste skalnings åtgärds loggarna:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Använd följande avsnitt för dessa frågor. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>En skalnings åtgärd genomfördes som jag inte förväntade mig

Kör först frågan för skalnings åtgärd för att hitta den skalnings åtgärd som du är intresse rad av. Om det är den senaste skalnings åtgärden använder du följande fråga:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Välj fältet CorrelationId i loggen för skalnings åtgärder. Använd CorrelationId för att hitta rätt utvärderings logg. Om du kör frågan nedan visas alla regler och villkor som har utvärderats som inledande i denna skalnings åtgärd.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Vilken profil orsakade en skalnings åtgärd?

En åtgärd som har skalats har inträffat, men du har överlappande regler och profiler och behöver spåra vilken som orsakade åtgärden. 

Hitta correlationId för skalnings åtgärden (enligt beskrivningen i exempel 1) och kör sedan frågan på utvärderings loggar för att lära dig mer om profilen.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

Utvärderingen av hela profilen kan också tolkas bättre med hjälp av följande fråga

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>En skalnings åtgärd utfördes inte

Jag förväntade en skalnings åtgärd och den utfördes inte. Det kan finnas inga skalnings åtgärds händelser eller loggar.

Granska måtten för autoskalning om du använder en Metric-baserad skalnings regel. Det är möjligt att det **observerade mått svärdet** eller den **observerade kapaciteten** inte är det du förväntade dig att du skulle vara och därför gick det inte att starta skalnings regeln. Du kan fortfarande se utvärderingar, men inte en skalbar regel. Det är också möjligt att den nedkylnings tid som behålls av en skalnings åtgärd inträffar. 

 Granska utvärderings loggarna för autoskalning under den tids period som du förväntar dig att skalnings åtgärden ska ske. Granska alla utvärderingar som det gjorde och varför det beslutade att inte utlösa en skalnings åtgärd.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Skalnings åtgärden misslyckades

Det kan finnas ett fall där den automatiska skalnings tjänsten vidtog åtgärden, men systemet beslutade inte att skala eller slutföra skalnings åtgärden. Använd den här frågan för att hitta fel skalnings åtgärder.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Skapa aviserings regler för att få meddelanden om automatiska skalnings åtgärder eller problem. Du kan också skapa aviserings regler för att få meddelanden om automatiska skalnings händelser.

## <a name="schema-of-autoscale-resource-logs"></a>Schema för resurs loggar för autoskalning

Mer information finns i [Autoskala resurs loggar](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>Nästa steg
Läs information om [bästa metoder för autoskalning](autoscale-best-practices.md). 
