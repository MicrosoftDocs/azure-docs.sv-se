---
title: Konfigurera övervakning av gäst hälsa för VM Insights med hjälp av data insamlings regler (för hands version)
description: Beskriver hur du ändrar standard övervakning i VM Insights-gäst hälsa i skala med hjälp av Resource Manager-mallar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 0db6ed7566c53429f8b9798ac8cdafe76ca7bd5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052151"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-using-data-collection-rules-preview"></a>Konfigurera övervakning av gäst hälsa för VM Insights med hjälp av data insamlings regler (för hands version)
Med [gäst hälsa för virtuella datorer](vminsights-health-overview.md) kan du Visa hälso tillståndet för en virtuell dator som definieras av en uppsättning prestanda mätningar som samplas med jämna mellanrum. Den här artikeln beskriver hur du kan ändra standard övervakning över flera virtuella datorer med hjälp av data insamlings regler.


## <a name="monitors"></a>Övervakare
Hälso tillståndet för en virtuell dator bestäms av [hälso](vminsights-health-overview.md#health-rollup-policy) tillståndet för varje övervakare. Det finns två typer av övervakare i gäst hälsa för VM Insights som visas i följande tabell.

| Monitor | Beskrivning |
|:---|:---|
| Enhetsövervakare | Mäter en viss aspekt av en resurs eller ett program. Det kan till exempel handla om att kontrollera en prestandaräknare för att fastställa resursens prestanda eller dess tillgänglighet. |
| Samlingsövervakare | Grupperar flera övervakare för att visa ett sammanställt hälsotillstånd. En samlingsövervakare kan innehålla en eller flera enhetsövervakare och andra samlingsövervakare. |

Den uppsättning Övervakare som används av gäst hälsa för VM Insights och deras konfiguration kan inte ändras direkt. Du kan skapa [åsidosättningar](#overrides) om du ändrar beteendet för standard konfigurationen. Åsidosättningar definieras i data insamlings regler. Du kan skapa flera data insamlings regler som innehåller flera åsidosättningar för att uppnå nödvändig övervaknings konfiguration.

## <a name="monitor-properties"></a>Övervaka egenskaper
I följande tabell beskrivs de egenskaper som kan konfigureras för varje övervakare.

| Egenskap | Övervakare | Beskrivning |
|:---|:---|:---|
| Enabled | Aggregera<br>Enhet | Om värdet är true beräknas tillstånds övervakaren och bidrar till den virtuella datorns hälso tillstånd. Det kan utlösa en avisering om aviseringar har Aktiver ATS. |
| Aviseringar | Aggregera<br>Enhet | Om värdet är true utlöses en avisering för övervakaren när den flyttas till ett ohälsosamt tillstånd. Om värdet är False kommer övervakaren fortfarande att bidra till hälso tillståndet för den virtuella datorn, vilket kan utlösa en avisering. |
| Varning | Enhet | Villkor för varnings tillstånd. Om ingen, kommer övervakaren aldrig att ange ett varnings tillstånd. |
| Kritiskt | Enhet | Villkor för kritiskt tillstånd. Om ingen, kommer övervakaren aldrig att ange ett kritiskt tillstånd. |
| Utvärderings frekvens | Enhet | Frekvens för hälso tillstånd som utvärderas. |
| Lookback | Enhet | Storleken på lookback-fönstret på några sekunder. Se [monitorConfiguration-elementet](#monitorconfiguration-element) för detaljerad beskrivning. |
| Utvärderings typ | Enhet | Definierar vilket värde som ska användas från exempel uppsättningen. Se [monitorConfiguration-elementet](#monitorconfiguration-element) för detaljerad beskrivning. |
| Minsta exempel | Enhet | Minsta antal värden som ska användas för att beräkna värdet. |
| Maximalt exempel | Enhet | Maximalt antal värden som ska användas för att beräkna värdet. |


## <a name="default-configuration"></a>Standardkonfiguration
I följande tabell visas standard konfigurationen för varje övervakare. Den här standard konfigurationen kan inte ändras direkt, men du kan definiera [åsidosättningar](#overrides) som ändrar övervaknings konfigurationen för vissa virtuella datorer.


| Monitor | Enabled | Aviseringar | Varning | Kritiskt | Utvärderings frekvens | Lookback | Utvärderings typ | Minsta exempel | Max. exempel |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| CPU-användning  | Sant | Falskt | Inget | \> 90%    | 60 sek. | 240 SEK | Min | 2 | 3 |
| Tillgängligt minne | Sant | Falskt | Inget | \< 100 MB | 60 sek. | 240 SEK | Max | 2 | 3 |
| Filsystem      | Sant | Falskt | Inget | \< 100 MB | 60 sek. | 120 SEK | Max | 1 | 1 |


## <a name="overrides"></a>Åsidosättningar
En *åsidosättning* ändrar en eller flera egenskaper för en övervakare. En åsidosättning kan till exempel inaktivera en Övervakare som är aktive rad som standard, definiera varnings villkor för övervakaren eller ändra övervakarens kritiska tröskel. 

Åsidosättningar definieras i en [data insamlings regel (DCR)](../agents/data-collection-rule-overview.md). Du kan skapa flera DCRs med olika uppsättningar av åsidosättningar och tillämpa dem på flera virtuella datorer. Du använder en DCR på en virtuell dator genom att skapa en Association enligt beskrivningen i [Konfigurera data insamling för Azure Monitor agenten (för hands version)](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations).


## <a name="multiple-overrides"></a>Flera åsidosättningar

En enskild övervakare kan ha flera åsidosättningar. Om åsidosättningarna definierar olika egenskaper, är den resulterande konfigurationen en kombination av alla åsidosättningar.

Övervakaren anger till exempel `memory|available` inte ett varnings tröskelvärde eller aktiverar avisering som standard. Tänk på följande åsidosättningar som tillämpas på den här övervakaren:

- Åsidosätt 1 definierar `alertConfiguration.isEnabled` egenskap svärdet som `true`
- Åsidosätt 2 definierar `monitorConfiguration.warningCondition` med ett tröskel villkor för `< 250` .

Den resulterande konfigurationen skulle vara en Övervakare som hamnar i ett hälso tillstånd för varningar när mindre än 250 MB minne är tillgängligt och skapar allvarlighets grad 2-aviseringar och också hamnar i kritiskt hälso tillstånd när mindre än 100 MB tillgängligt minne är tillgängligt och skapar allvarlighets grad 1 (eller ändrar en befintlig avisering från allvarlighets grad 2 till 1 om den redan finns).

Om två åsidosättningar definierar samma egenskap på samma övervakare, kommer ett värde att prioriteras. Åsidosättningar tillämpas utifrån deras [omfång](#scopes-element), från den mest allmänna till den mest aktuella. Det innebär att de mest exakta åsidosättningarna kommer att ha störst chans att tillämpas. Den angivna ordningen är följande:

1. Global 
2. Prenumeration
3. Resursgrupp
4. Virtuell dator. 

Om flera åsidosättningar på samma omfattnings nivå definierar samma egenskap i samma övervakare, tillämpas de i den ordning som de visas i DCR. Om åsidosättningarna finns i olika DCRs tillämpas de i alfabetisk ordning i DCR-resurs-ID: n.


## <a name="data-collection-rule-configuration"></a>Konfiguration av data insamlings regel
JSON-elementen i data insamlings regeln som definierar åsidosättningar beskrivs i följande avsnitt. Ett fullständigt exempel finns i [regeln för insamling av exempel data](#sample-data-collection-rule).

## <a name="extensions-structure"></a>tilläggs struktur
Gäst hälsa implementeras som ett tillägg till Azure Monitor Agent, så åsidosättningar definieras i `extensions` elementet i data insamlings regeln. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| Element | Krävs | Beskrivning |
|:---|:---|:---|
| `name` | Ja | Användardefinierad sträng för tillägget. |
| `streams` | Ja | Lista över strömmar som gäst hälso data ska skickas till. Detta måste innehålla **Microsoft-HealthStateChange**.  |
| `extensionName` | Ja | Namn på tillägget. Detta måste vara **HealthExtension**. |
| `extensionSettings` | Ja | Matris med `healthRuleOverride` element som ska användas i standard konfigurationen. |


## <a name="extensionsettings-element"></a>extensionSettings-element
Innehåller inställningar för tillägget.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| Element | Krävs | Beskrivning |
|:---|:---|:---|
| `schemaVersion` | Ja | Strängen som definieras av Microsoft som representerar det förväntade schemat för elementet. Måste för närvarande vara inställt på 1,0 |
| `contentVersion` | Inga | Sträng som definieras av användaren för att spåra olika versioner av hälso konfigurationen, om det behövs. |
| `healthRuleOverrides` | Ja | Matris med `healthRuleOverride` element som ska användas i standard konfigurationen. |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides-element
Innehåller ett eller flera `healthRuleOverride` element som varje definierar en åsidosättning.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| Element | Krävs | Beskrivning |
|:---|:---|:---|
| `scopes` | Ja | Lista över en eller flera omfattningar som anger de virtuella datorer som den här åsidosättningen gäller för. Även om DCR-filen är associerad med en virtuell dator måste den virtuella datorn falla inom ett omfång för att åsidosättningen ska tillämpas. |
| `monitors` | Ja | Lista över en eller flera strängar som definierar vilka Övervakare som får den här åsidosättningen.  |
| `monitorConfiguration` | Inga | Konfiguration av övervakaren inklusive hälso tillstånd och hur de beräknas. |
| `alertConfiguration` | Inga | Aviserings konfiguration för övervakaren. |
| `isEnabled` | Inga | Anger om övervakaren är aktive rad eller inte. Inaktiverade övervaknings växlar till särskilt *inaktiverat* hälso tillstånd och tillstånd inaktiverade om de inte aktive ras igen Om den utelämnas kommer övervakaren att ärva sin status från överordnad övervakare i hierarkin. |


## <a name="scopes-element"></a>element för omfattning
Varje åsidosättning har en eller flera omfattningar som definierar vilka virtuella datorer som åsidosättningen ska tillämpas på. Omfånget kan vara en prenumeration, en resurs grupp eller en enskild virtuell dator. Även om åsidosättningen finns i en DCR som är kopplad till en viss virtuell dator, tillämpas den endast på den virtuella datorn om den virtuella datorn är inom en av åsidosättningens omfång. På så sätt kan du på ett stort sätt associera ett mindre antal DCRs till en uppsättning virtuella datorer, men ger detaljerad kontroll över tilldelningen av varje åsidosättning i själva DCR-filen. Du kanske vill skapa en liten uppsättning DCRs och koppla dem till en uppsättning virtuella datorer med hjälp av en princip när du anger åsidosättningar för hälso övervakaren för olika del mängder av de virtuella datorerna med hjälp av elementet scope.

I följande tabell visas exempel på olika omfång.

| Omfång | Exempel |
|:---|:---|
| Enskild virtuell dator | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| Alla virtuella datorer i en resurs grupp | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| Alla virtuella datorer i en prenumeration | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| Alla virtuella datorer som data insamlings regeln är associerad med | `*` |


## <a name="monitors-element"></a>övervakar element
Lista över en eller flera strängar som definierar vilka övervakare i hälsohierarkin som får den här åsidosättningen. Varje-element kan vara ett övervaknings namn eller typnamn som matchar en eller flera Övervakare som får den här åsidosättningen. 

```json
"monitors": [
    "<monitor name>"
 ],
```



I följande tabell visas de aktuella tillgängliga övervakarens namn.

| Typnamn | Name | Beskrivning |
|:---|:---|:---|
| skogen | skogen | Övervakaren på den översta nivån representerar hälsa för den virtuella datorn. | |
| processor användning | processor användning | Övervakaren för processor användning. | |
| logiska diskar | logiska diskar | Sammanställd Övervakare för hälso tillstånd för alla övervakade diskar på virtuella Windows-datorer. | |
| logiska diskar\|* | logiska diskar \| C:<br>logiska diskar \| D: | Sammanställd Övervakare för spårnings hälsa för en specifik disk på en virtuell Windows-dator. | 
| \| * \| ledigt utrymme på logisk disk | logiska diskar \| C: \| ledigt utrymme<br>logiska diskar \| D: \| ledigt utrymme | Övervakaren ledigt disk utrymme på den virtuella Windows-datorn. |
| fil system | fil system | Sammanställd Övervakare för hälso tillstånd för alla fil system på virtuella Linux-datorer. |
| fil system\|* | fil system\|/<br>fil system \| /mnt | Sammanställd Övervakare för spårning av ett fil system med en virtuell Linux-dator. | fil system|/var/log |
| \| * \| ledigt utrymme i fil system | \| / \| ledigt utrymme i fil system<br>fil system \| /mnt \| ledigt utrymme | Övervakning av ledigt disk utrymme på fil systemet för virtuella Linux-datorer. | 
| minne | minne | Sammanställd Övervakare för hälso tillståndet för det virtuella dator minnet. | |
| \|tillgängligt minne| \|tillgängligt minne | Övervaka spårning av tillgängligt minne på den virtuella datorn. | |


## <a name="alertconfiguration-element"></a>alertConfiguration-element
Anger om en avisering ska skapas från övervakaren.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| Element | Obligatorisk | Beskrivning | 
|:---|:---|:---|
| `isEnabled` | Nej | Om värdet är true genererar övervakaren en avisering när du växlar till ett kritiskt eller varnings tillstånd och löser aviseringen när du återgår till felfritt tillstånd. Om värdet är false eller utelämnas genereras ingen avisering.  |


## <a name="monitorconfiguration-element"></a>monitorConfiguration-element
Gäller endast för enhets övervakare. Definierar konfigurationen för övervakaren inklusive hälso tillstånd och hur de beräknas.

Parametrar definierar algoritmen för att beräkna måttets värde som ska jämföras mot tröskelvärden. I stället för att agera på ett exempel på data från underliggande mått utvärderar övervakaren flera mått exempel som tas emot i fönstret från utvärderings tiden och `lookbackSec` sedan. Alla exempel som tas emot inom denna tidsram beaktas och om antalet prover är större än `maxSamples` , kommer äldre exempel att `maxSamples` ignoreras. 

Om det finns mindre exempel i lookback intervall än `minSamples` , kommer övervakaren att växla in till ett *Okänt* hälso tillstånd som anger att det inte finns tillräckligt med data för att fatta välgrundade beslut om hälso tillståndet för underliggande mått. Om fler sampel `minSamples` är tillgängliga, körs en agg regerings funktion som anges av evaluationType-parametern som USA kör över uppsättningen för att beräkna ett enda värde.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| Element | Obligatorisk | Beskrivning | 
|:---|:---|:---|
| `evaluationFrequencySecs` | Nej | Definierar frekvensen för utvärdering av hälso tillstånd. Varje övervakare utvärderas när agenten startar och enligt ett vanligt intervall som definieras av den här parametern därefter. |
| `lookbackSecs`   | Inga | Storleken på lookback-fönstret på några sekunder. |
| `evaluationType` | Inga | `min` – ta minsta värdet från hela exempel uppsättningen<br>`max` -ta Max värdet från hela exempel uppsättningen<br>`avg` – ta medelvärdet av exempel uppsättnings värden<br>`all` – jämför varje enskilt värde i uppsättningen med tröskelvärden. Övervaka växlar tillstånd om och endast om alla exempel i uppsättningen uppfyller tröskel villkor. |
| `minSamples`     | Inga | Minsta antal värden som ska användas för att beräkna värdet. |
| `maxSamples`     | Inga | Maximalt antal värden som ska användas för att beräkna värdet. |
| `warningCondition`  | Inga | Tröskel-och jämförelse logik för varnings villkoret. |
| `criticalCondition` | Inga | Tröskel och jämförelse logik för kritiskt villkor. |


## <a name="warningcondition-element"></a>warningCondition-element
Definierar tröskel och jämförelse logik för varnings villkoret. Om det här elementet inte ingår, kommer övervakaren aldrig att växla till hälso tillståndet varning.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Egenskap | Obligatorisk | Beskrivning | 
|:---|:---|:---|
| `isEnabled` | Nej | Anger om villkor är aktiverat. Om värdet är **false** inaktive ras villkoret även om tröskelvärdet och operatörens egenskaper kan anges. |
| `threshold` | Inga | Definierar tröskel för jämförelse av utvärderat värde. |
| `operator`  | Inga | Definierar jämförelse operatorn som ska användas i tröskel uttryck. Möjliga värden: >, <, >=, <=, = =. |


## <a name="criticalcondition-element"></a>criticalCondition-element
Definierar tröskel och jämförelse logik för kritiskt villkor. Om det här elementet inte ingår, kommer övervakaren aldrig att växla till kritiskt hälso tillstånd.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Egenskap | Obligatorisk | Beskrivning | 
|:---|:---|:---|
| `isEnabled` | Nej | Anger om villkor är aktiverat. Om värdet är **false** inaktive ras villkoret även om tröskelvärdet och operatörens egenskaper kan anges. |
| `threshold` | Inga | Definierar tröskel för jämförelse av utvärderat värde. |
| `operator`  | Inga | Definierar jämförelse operatorn som ska användas i tröskel uttryck. Möjliga värden: >, <, >=, <=, = =. |

## <a name="sample-data-collection-rule"></a>Exempel på data insamlings regel
En exempel på data insamlings regel som aktiverar gäst övervakning finns i [Aktivera en virtuell dator med Resource Manager-mall](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template).


## <a name="next-steps"></a>Nästa steg

- Läs mer om [data insamlings regler](../agents/data-collection-rule-overview.md).