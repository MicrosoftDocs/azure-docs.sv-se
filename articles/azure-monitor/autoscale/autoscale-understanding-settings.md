---
title: Förstå inställningarna för autoskalning i Azure Monitor
description: En detaljerad analys av inställningarna för autoskalning och hur de fungerar. Gäller för Virtual Machines Cloud Services Web Apps
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: a914f6d71c013acea8dfde0f6578985bc009bb26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100623807"
---
# <a name="understand-autoscale-settings"></a>Förstå inställningarna för automatisk skalning
Med inställningarna för automatisk skalning kan du se till att du har rätt mängd resurser som körs för att hantera belastningen på ditt program. Du kan konfigurera inställningar för automatisk skalning som ska utlösas baserat på mått som indikerar belastning eller prestanda eller som utlöses vid schemalagd tidpunkt och tidpunkt. Den här artikeln tar en detaljerad titt på konfigurationen av en inställning för autoskalning. Artikeln börjar med schema och egenskaper för en inställning och går sedan igenom de olika profil typerna som kan konfigureras. Slutligen diskuterar artikeln hur funktionen för autoskalning i Azure utvärderar vilken profil som ska köras vid en specifik tidpunkt.

## <a name="autoscale-setting-schema"></a>Schema för autoskalningsinställning
För att illustrera schemat för autoskalning av inställningar används följande inställningar för autoskalning. Det är viktigt att Observera att den här inställningen för autoskalning har:
- En profil. 
- Två mått regler i den här profilen: en för utskalning och en för skalning i.
  - Den skalbara regeln utlöses när processor måttet för den genomsnittliga procent andelen för virtuell dator är större än 85 procent under de senaste 10 minuterna.
  - Skalnings regeln utlöses när den virtuella datorns skal uppsättnings genomsnitt är mindre än 60 procent under den senaste minuten.

> [!NOTE]
> En inställning kan ha flera profiler. Mer information finns i avsnittet om [profiler](#autoscale-profiles) . En profil kan också ha flera definierade regler och skalnings regler. Information om hur de utvärderas finns i avsnittet [utvärdering](#autoscale-evaluation) .

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Avsnitt | Elementnamn | Beskrivning |
| --- | --- | --- |
| Inställning | ID | Resurs-ID för den autoskalningsinställning. Inställningarna för autoskalning är en Azure Resource Manager resurs. |
| Inställning | name | Namnet på den automatiska skalnings inställningen. |
| Inställning | location | Platsen för den automatiska skalnings inställningen. Den här platsen kan skilja sig från platsen för den resurs som skalas. |
| properties | targetResourceUri | Resurs-ID för den resurs som skalas. Du kan bara ha en inställning för autoskalning per resurs. |
| properties | filer | En inställning för autoskalning består av en eller flera profiler. Varje gången den automatiska skalnings motorn körs körs en profil. |
| profil | name | Namnet på profilen. Du kan välja ett namn som hjälper dig att identifiera profilen. |
| profil | Kapacitet. maximum | Den maximalt tillåtna kapaciteten. Det säkerställer att den automatiska skalningen, när den här profilen körs, inte skalar din resurs ovanför det här talet. |
| profil | Kapacitet. minimum | Den minsta tillåtna kapaciteten. Det säkerställer att den automatiska skalningen, när den här profilen körs, inte skalar din resurs under det här talet. |
| profil | Kapacitet. standard | Om det uppstår problem med att läsa resurs måttet (i det här fallet är CPU: n för "vmss1") och den aktuella kapaciteten är lägre än standardinställningen, skalar automatiskt ut till standardvärdet. Detta är för att säkerställa resursens tillgänglighet. Om den aktuella kapaciteten redan är högre än standard kapaciteten skalas inte autoskalning i. |
| profil | regler | Automatisk skalning skalar automatiskt mellan högsta och lägsta kapacitet genom att använda reglerna i profilen. Du kan ha flera regler i en profil. Det finns vanligt vis två regler: en för att bestämma när du ska skala ut och den andra för att bestämma när du ska skala in. |
| allmänhet | metricTrigger | Definierar regelns mått villkor. |
| metricTrigger | metricName | Namnet på måttet. |
| metricTrigger |  metricResourceUri | Resurs-ID för den resurs som avger måttet. I de flesta fall är det samma som den resurs som skalas. I vissa fall kan det vara olika. Du kan till exempel skala en skalnings uppsättning för virtuella datorer baserat på antalet meddelanden i en lagrings kö. |
| metricTrigger | timeGrain | Mått samplingens varaktighet. Till exempel innebär **TimeGrain = "PT1M"** att måtten ska aggregeras var 1 minut, genom att använda den agg regerings metod som anges i elementet statistik. |
| metricTrigger | statistik | Agg regerings metoden inom timeGrain-perioden. Till exempel, **statistik = "Average"** och **TIMEGRAIN = "PT1M"** innebär att måtten ska aggregeras var 1 minut, genom att ta medelvärdet. Den här egenskapen anger hur måttet ska samplas. |
| metricTrigger | timeWindow | Hur lång tid det får ta att se upp för Mät värden. Exempel: **timeWindow = "PT10M"** innebär att varje gång autoskalning körs, frågar den efter de senaste 10 minuterna. Tidsfönstret gör att dina mått är normaliserade och förhindrar att de reagerar på tillfälliga toppar. |
| metricTrigger | timeAggregation | Den agg regerings metod som används för att aggregera exempel måtten. Till exempel ska **TimeAggregation = "Average"** summera de samplade måtten genom att ta medelvärdet. I föregående fall tar du med 10 1-minuters provet och beräknar det. |
| allmänhet | scaleAction | Den åtgärd som ska vidtas när metricTrigger för regeln utlöses. |
| scaleAction | riktning | "Öka" för att skala ut eller "minska" för att skala in.|
| scaleAction | värde | Hur mycket du kan öka eller minska resursens kapacitet. |
| scaleAction | cooldown | Hur lång tid som ska förflyta efter en skalnings åtgärd innan skalning igen. Om t. ex. **cooldown = "PT10M"** försöker autoskalning inte skala igen under en period om 10 minuter. Cooldown är att tillåta att måtten stabiliseras efter tillägg eller borttagning av instanser. |

## <a name="autoscale-profiles"></a>Autoskalning av profiler

Det finns tre typer av profiler för autoskalning:

- **Vanlig profil:** Den vanligaste profilen. Om du inte behöver skala resursen baserat på vecko dagen eller en viss dag kan du använda en vanlig profil. Den här profilen kan sedan konfigureras med mått regler som styr när de ska skalas ut och när de ska skalas i. Du bör bara ha en definierad reguljär profil.

    Exempel profilen som användes tidigare i den här artikeln är ett exempel på en vanlig profil. Observera att du också kan ange en profil för skalning till ett statiskt antal instanser för din resurs.

- **Fast datum profil:** Den här profilen är för särskilda fall. Anta till exempel att du har en viktig händelse som kommer upp den 26 december 2017 (PST). Du vill att den minsta och högsta kapaciteten för din resurs ska vara en annan dag, men fortfarande skala på samma mått. I det här fallet bör du lägga till en fast datum profil i din inställnings lista med profiler. Profilen är konfigurerad att endast köras på händelsens dag. För en annan dag använder autoskalning den vanliga profilen.

    ```json
    "profiles": [
        {
            "name": " regularProfile",
            "capacity": {
                ...
            },
            "rules": [
                {
                ...
                },
                {
                ...
                }
            ]
        },
        {
            "name": "eventProfile",
            "capacity": {
            ...
            },
            "rules": [
                {
                ...
                }, 
                {
                ...
                }
            ],
            "fixedDate": {
                "timeZone": "Pacific Standard Time",
                "start": "2017-12-26T00:00:00",
                "end": "2017-12-26T23:59:00"
            }
        }
    ]
    ```
    
- **Upprepnings profil:** Med den här typen av profil kan du se till att profilen alltid används på en viss dag i veckan. Upprepnings profiler har bara en start tid. De körs tills nästa upprepnings profil eller fasta datum profil är inställd på Starta. En inställning för autoskalning med endast en upprepnings profil kör profilen, även om det finns en vanlig profil definierad i samma inställning. I följande två exempel visas hur den här profilen används:

    **Exempel 1: vardagar kontra helger**
    
    Anta att du vill att din maximala kapacitet ska vara 4. På vardagar, eftersom du förväntar dig mer belastning, vill du att din maximala kapacitet ska vara 10. I det här fallet skulle din inställning innehålla två upprepnings profiler, en att köras på helger och den andra på vardagar.
    Inställningen ser ut så här:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    Inställningen ovan visar att varje upprepnings profil har ett schema. Det här schemat avgör när profilen börjar köras. Profilen stoppas när det är dags att köra en annan profil.

    I den föregående inställningen är "weekdayProfile" inställd på att starta på måndag vid 12:00 AM. Det innebär att den här profilen börjar köras på måndag vid 12:00. Den fortsätter till lördag kl. 12:00 när "weekendProfile" är schemalagd för att börja köras.

    **Exempel 2: kontors tid**
    
    Anta att du vill ha ett mått tröskelvärde under kontors tid (9:00 till 5:00 PM) och ett annat för alla andra tider. Inställningen skulle se ut så här:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Inställningen ovan visar att "businessHoursProfile" börjar köras på måndag kl. 9:00 AM och fortsätter till 5:00 PM. Det är när "nonBusinessHoursProfile" börjar köras. "NonBusinessHoursProfile" körs fram till 9:00 AM tisdag och sedan tar "businessHoursProfile" över igen. Detta upprepas tills fredag på 5:00 PM. Vid den tidpunkten körs "nonBusinessHoursProfile" hela vägen till måndag kl. 9:00.
    
> [!Note]
> Gränssnittet för automatisk skalning i Azure Portal tillämpar slut tider för upprepnings profiler och börjar köra den autoskalningsinställning som är standard profil i mellan upprepnings profilerna.
    
## <a name="autoscale-evaluation"></a>Autoskalning av utvärdering
Eftersom inställningarna för autoskalning kan ha flera profiler, och varje profil kan ha flera mått regler, är det viktigt att förstå hur en inställning för autoskalning utvärderas. Varje gången det automatiska skalnings jobbet körs börjar det genom att välja den profil som är tillämplig. Autoskalning utvärderar sedan de lägsta och högsta värdena och eventuella mått regler i profilen och bestämmer om en skalnings åtgärd krävs.

### <a name="which-profile-will-autoscale-pick"></a>Vilken profil ska autoskalning väljas?

Autoskalning använder följande sekvens för att välja profilen:
1. Först letar den upp en fast datum profil som är konfigurerad för att köras nu. Om det finns det körs den automatiska skalningen. Om det finns flera fasta datum profiler som ska köras väljer autoskalning den första.
2. Om det inte finns några fasta datum profiler tittar autoskalning på upprepnings profilerna. Om en upprepnings profil hittas körs den.
3. Om det inte finns några fasta datum-eller upprepnings profiler kör autoskalning den vanliga profilen.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hur utvärderar autoskalning flera regler?

När den automatiska skalningen bestämmer vilken profil som ska köras, utvärderar den alla skalnings regler i profilen (de är regler med **riktningen = "öka"**).

Om en eller flera skalnings regler utlöses beräknar autoskalning den nya kapaciteten som bestäms av **scaleAction** för var och en av dessa regler. Sedan skalas de ut till det maximala antalet av dessa kapaciteter för att säkerställa tjänstens tillgänglighet.

Anta till exempel att det finns en skalnings uppsättning för virtuella datorer med en aktuell kapacitet på 10. Det finns två skalbara regler: en som ökar kapaciteten med 10 procent och en som ökar kapaciteten med 3 antal. Den första regeln skulle resultera i en ny kapacitet på 11, och den andra regeln skulle leda till en kapacitet på 13. För att säkerställa tjänstens tillgänglighet väljer autoskalning den åtgärd som resulterar i Max kapaciteten, så den andra regeln väljs.

Om inga skalnings regler utlöses utvärderar autoskalning alla skalnings regler (regler med **riktningen = "minska"**). Autoskalning tar bara en skalnings åtgärd om alla regler för skalning utlöses.

Autoskalning beräknar den nya kapaciteten som bestäms av **scaleAction** för var och en av dessa regler. Sedan väljer den skalnings åtgärd som resulterar i max för de kapaciteten för att säkerställa tjänstens tillgänglighet.

Anta till exempel att det finns en skalnings uppsättning för virtuella datorer med en aktuell kapacitet på 10. Det finns två skalnings regler: en som minskar kapaciteten med 50 procent och en som minskar kapaciteten med 3 antal. Den första regeln skulle resultera i en ny kapacitet på 5, och den andra regeln skulle resultera i en kapacitet på 7. För att säkerställa tjänstens tillgänglighet väljer autoskalning den åtgärd som resulterar i Max kapaciteten, så den andra regeln väljs.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om autoskalning genom att referera till följande:

* [Översikt över automatisk skalning](./autoscale-overview.md)
* [Azure Monitor vanliga mått för autoskalning](./autoscale-common-metrics.md)
* [Metodtips för autoskalning i Azure Monitor](./autoscale-best-practices.md)
* [Använda åtgärder för autoskalning för att skicka aviseringar via e-post och webhook](./autoscale-webhook-email.md)
* [Skala REST API](/rest/api/monitor/autoscalesettings)

