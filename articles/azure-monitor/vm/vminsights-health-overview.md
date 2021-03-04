---
title: Gäst hälsa för VM Insights (förhands granskning)
description: Översikt över hälso funktionen i VM Insights, inklusive hur du kan visa hälso tillståndet för dina virtuella datorer och få aviseringar när en virtuell dator blir ohälsosam.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 2922ca4068531c45e6acad0ce54aa96624c6238e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052134"
---
# <a name="vm-insights-guest-health-preview"></a>Gäst hälsa för VM Insights (förhands granskning)
Med gäst hälsa för virtuella datorer kan du Visa hälso tillståndet för virtuella datorer baserat på en uppsättning prestanda mätningar som samplas med jämna mellanrum från gäst operativ systemet. Du kan snabbt kontrol lera hälsan för alla virtuella datorer i en prenumeration eller resurs grupp, öka detalj nivån i den detaljerade hälsan för en viss virtuell dator eller proaktivt under rättas när en virtuell dator blir ohälsosam. 

## <a name="enable-virtual-machine-health"></a>Aktivera hälsa för virtuell dator
Se [Aktivera VM Insights-gäst hälsa (för hands version)](vminsights-health-enable.md) för information om hur du aktiverar funktionen för gäst hälsa och onboarding av virtuella datorer.

## <a name="pricing"></a>Prissättning
Det finns ingen direkt kostnad för funktionen för gäst hälsa, men det finns en kostnad för att mata in och lagra hälso tillstånds data i Log Analytics-arbetsytan. Alla data lagras i tabellen *HealthStateChangeEvent* . Mer information om pris modeller och kostnader finns i [Hantera användning och kostnader med Azure Monitor loggar](../logs/manage-cost-storage.md) .

## <a name="view-virtual-machine-health"></a>Visa hälsa för virtuell dator
Kolumnen **gäst hälsa för virtuell dator** på sidan **Kom igång** ger dig en snabb överblick över hälso tillståndet för varje virtuell dator i en viss prenumeration eller resurs grupp. Den aktuella hälsan för varje virtuell dator visas medan ikoner för varje grupp visar antalet virtuella datorer för närvarande i varje tillstånd i gruppen.

[![Sidan kom igång med gäst-VM-hälsa](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>Övervakare
Klicka på en virtuell dators hälso status om du vill visa detaljerad status för var och en av dess övervakare. Varje övervakare mäter hälsan för en viss komponent. Den övergripande hälso tillståndet för den virtuella datorn bestäms av hälso tillståndet för de enskilda övervakarna. 

![Exempel på bildskärmar](media/vminsights-health-overview/monitors.png)

I följande tabell visas de mängd-och enhets Övervakare som för närvarande är tillgängliga för varje virtuell dator. 

| Monitor | Typ | Beskrivning |
|:---|:---|:---|
| CPU-användning | Enhet | Processor användning i procent. |
| Filsystem | Aggregera | Sammanställd hälsa för alla fil system på virtuella Linux-datorer. |
| Filsystem  | Aggregera | Hälso tillståndet för varje enskilt fil system på den virtuella Linux-datorn. Namnet på övervakaren är namnet på fil systemet. |
| Ledigt utrymme | Enhet | Procent ledigt utrymme i fil systemet. |
| Logiska diskar | Aggregera | Sammanställd hälsa för alla logiska diskar på virtuell Windows-dator. |
| Logisk disk  | Aggregera | Hälso tillståndet för varje enskild logisk disk på en virtuell Windows-dator. Namnet på övervakaren är namnet på disken. |
| Minne | Aggregera | Aggregera hälsan för minnet på den virtuella datorn. |
| Tillgängligt minne | Enhet | Tillgängliga megabyte på den virtuella datorn. |

## <a name="health-states"></a>Hälso tillstånd
Varje övervaknings exempel innehåller värden på agenten varje minut och jämför exempel värden med villkoren för varje hälso tillstånd. Om kriteriet för ett visst tillstånd är sant anges övervakaren till detta tillstånd. Om inget av villkoren är sant är övervakaren inställt på felfritt tillstånd. Data skickas från agenten till Azure Monitor var tredje minut eller omedelbart om det sker en tillstånds ändring.

Varje övervakare har ett lookback-fönster och analyserar alla exempel som samlas in inom den tiden. En övervakare kan till exempel ha ett lookback-fönster på 240 sekunder som söker efter det högsta värdet bland minst 2 exempel värden, men inte fler än de senaste 3. Medan värden samplas med jämna mellanrum kan antalet sampel i ett visst fönster variera något om det uppstår avbrott i agent åtgärden.

Övervaka var och en har de potentiella hälso tillstånden i följande tabell och kommer att vara i en och endast en specifik tidpunkt. När en övervakare initieras startar den i felfritt tillstånd.

| Hälsotillstånd | Beskrivning |
|:---|:---|
| Felfri  | Övervakaren överskrider för närvarande inte varningen eller det kritiska tröskelvärdet. |
| Varning  | Övervakaren överskred varnings tröskelvärdet (om det har definierats). |
| Kritiskt | Övervakaren överskred det kritiska tröskelvärdet (om det har definierats). |
| Okänt  | Inte tillräckligt med data har samlats in för att fastställa hälso tillståndet. |
| Inaktiverad | Övervakaren är för närvarande inaktive rad. |
| Inget     | Övervakaren har bara startats och ännu inte utvärderats eller det övervakade objektet finns inte längre. |



Det finns två typer av Övervakare som visas i följande tabell.

| Monitor | Beskrivning |
|:---|:---|
| Enhetsövervakare | Mäter en viss aspekt av en resurs eller ett program. Det kan till exempel handla om att kontrollera en prestandaräknare för att fastställa resursens prestanda eller dess tillgänglighet. |
| Samlingsövervakare | Grupperar flera övervakare för att visa ett sammanställt hälsotillstånd. En samlingsövervakare kan innehålla en eller flera enhetsövervakare och andra samlingsövervakare. |


  
### <a name="health-rollup-policy"></a>Princip för hälso sammanslagning
Hälso tillståndet för en virtuell dator bestäms av hälso tillståndet för varje enhet och aggregerade övervakare. Varje sammanställd övervakare använder en hälso sammanslagnings princip för sämsta tillstånd där tillståndet för den sammanställda övervakaren matchar tillståndet för den underordnade övervakaren med sämst hälso tillstånd.  

I följande exempel är övervakaren av **ledigt utrymme** i ett kritiskt tillstånd som skapar upp till mängderna för dess instans och sedan till **fil system**. Även om **processor användningen** används i varnings tillstånd är den virtuella datorn inställd på kritisk eftersom detta är det sämsta läget under den. Om det lediga utrymmet skulle återgå till ett felfritt tillstånd, kommer den virtuella datorn att ändra till ett varnings tillstånd sedan CPU-användningen skulle bli övervakare med sämsta tillstånd.

![Exempel på hälso sammanslagning](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>Information om övervakare
Välj en Övervakare för att visa information som innehåller följande flikar.

**Översikt** visar en beskrivning av övervakaren, den senaste gången den utvärderades och värden som samplats för att fastställa det aktuella hälso tillståndet. Antalet exempel kan variera beroende på definitionen av övervakaren och flyktiga av värdena.

[![Översikt över övervaknings information](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

**Historik** visar historiken för tillstånds ändringar för övervakaren. Du kan expandera alla tillstånds ändringar för att visa värden som utvärderas för att fastställa hälso tillståndet. Klicka på **Visa konfiguration används** för att visa konfigurationen av övervakaren vid den tidpunkt då hälso tillståndet ändrades.



[![Övervaka informations historik](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>Konfiguration
Visa och ändra konfigurationen för övervakaren för den valda virtuella datorn. Mer information finns i [Konfigurera övervakning av gäst hälsa för VM Insights (för hands version)](vminsights-health-enable.md) .

[![Konfiguration av övervaka Detaljer](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>Nästa steg

- [Aktivera gäst hälsa i VM Insights och inbyggda agenter.](vminsights-health-enable.md)
- [Konfigurera övervakare med hjälp av Azure Portal.](vminsights-health-configure.md)
- [Konfigurera övervakare med hjälp av data insamlings regler.](vminsights-health-configure-dcr.md)