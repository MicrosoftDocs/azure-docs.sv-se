---
title: Kapacitet och prestanda lösning i Azure Monitor | Microsoft Docs
description: Använd Kapacitet och prestanda lösning i Övervakaren för att hjälpa dig att förstå kapaciteten hos dina Hyper-V-servrar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: aa08b9f5db5e6371bcd20b473a0c755db3199e7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704301"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Planera kapaciteten för virtuella Hyper-V-datorer med Kapacitet och prestanda-lösningen (inaktuell)

![Kapacitet och prestanda symbol](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Den Kapacitet och prestanda lösningen är föråldrad.  Kunder som redan har installerat lösningen kan fortsätta att använda den, men Kapacitet och prestanda kan inte läggas till i nya arbets ytor.

Du kan använda Kapacitet och prestanda lösning i Övervakaren för att hjälpa dig att förstå kapaciteten hos dina Hyper-V-servrar. Lösningen ger insikter i din Hyper-V-miljö genom att visa den övergripande användningen (processor, minne och disk) för värdarna och de virtuella datorer som körs på dessa Hyper-V-värdar. Mått samlas in för CPU, minne och diskar över alla dina värdar och de virtuella datorer som körs på dem.

Lösningen:

-   Visar värdar med högsta och lägsta processor-och minnes användning
-   Visar virtuella datorer med högsta och lägsta processor-och minnes användning
-   Visar virtuella datorer med högsta och lägsta IOPS och data flödes användning
-   Visar vilka virtuella datorer som körs på vilka värdar
-   Visar de vanligaste diskarna med högt data flöde, IOPS och svars tid i klusterdelade volymer
- Gör att du kan anpassa och filtrera baserat på grupper

> [!NOTE]
> Den tidigare versionen av Kapacitet och prestanda-lösningen som kallas för kapacitets hantering krävde både System Center Operations Manager och System Center Virtual Machine Manager. Den här uppdaterade lösningen har inte dessa beroenden.


## <a name="connected-sources"></a>Anslutna källor

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Support | Beskrivning |
|---|---|---|
| [Windows-agenter](../agents/agent-windows.md) | Ja | Lösningen samlar in information om kapacitet och prestanda data från Windows-agenter. |
| [Linux-agenter](../vm/quick-collect-linux-computer.md) | Inga    | Lösningen samlar inte in information om kapacitet och prestanda data från Direct Linux-agenter.|
| [SCOM-hanterings grupp](../agents/om-agents.md) | Ja |Lösningen samlar in kapacitets-och prestanda data från agenter i en ansluten SCOM-hanterings grupp. Det krävs ingen direkt anslutning från SCOM agent till Log Analytics.|
| [Azure Storage-konto](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Inga | Azure Storage omfattar inte kapacitets-och prestanda data.|

## <a name="prerequisites"></a>Förutsättningar

- Windows-eller Operations Manager-agenter måste installeras på Windows Server 2012 eller högre Hyper-V-värdar, inte virtuella datorer.


## <a name="configuration"></a>Konfiguration

Utför följande steg för att lägga till Kapacitet och prestanda-lösningen på din arbets yta.

- Lägg till Kapacitet och prestanda lösning i arbets ytan Log Analytics med processen som beskrivs i [Lägg till Log Analytics lösningar från Lösningsgalleriet](./solutions.md).

## <a name="management-packs"></a>Hanteringspaket

Om din SCOM Management-grupp är ansluten till din Log Analytics-arbetsyta installeras följande hanterings paket i SCOM när du lägger till den här lösningen. Det krävs ingen konfigurering eller underhåll av dessa hanteringspaket.

- Microsoft. IntelligencePacks. CapacityPerformance

1201-händelsen liknar:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

När Kapacitet och prestandas lösningen uppdateras, kommer versions numret att ändras.

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../agents/om-agents.md).

## <a name="using-the-solution"></a>Använda lösningen

När du lägger till Kapacitet och prestanda-lösningen i din arbets yta läggs Kapacitet och prestanda till på översikts instrument panelen. Den här panelen visar ett antal aktiva Hyper-V-värdar och antalet aktiva virtuella datorer som övervakades för den valda tids perioden.

![Kapacitet och prestanda panel](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Granska användning

Öppna instrument panelen Kapacitet och prestanda genom att klicka på panelen Kapacitet och prestanda. Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn listar upp till tio objekt som matchar denna kolumns kriterier för specificerat omfång och tidsintervall. Du kan köra en loggsökning som returnerar alla poster genom att klicka på **Se alla** längst ned i kolumnen eller genom att klicka på kolumnrubriken.

- **Värdar**
    - **Processor användning för värd** Visar en grafisk trend för processor användningen för värddatorer och en lista över värdar, baserat på den valda tids perioden. Hovra över linje diagrammet om du vill visa information om en viss tidpunkt. Klicka på diagrammet om du vill visa mer information i loggs ökningen. Klicka på valfritt värdnamn om du vill öppna loggs ökning och Visa information om processor räknare för virtuella datorer som är värdar.
    - **Minnes användning för värd** Visar en grafisk trend för minnes användningen av värddatorer och en lista över värdar, baserat på den valda tids perioden. Hovra över linje diagrammet om du vill visa information om en viss tidpunkt. Klicka på diagrammet om du vill visa mer information i loggs ökningen. Klicka på alla värdnamn för att öppna loggs ökning och Visa minnes räknar information för virtuella datorer som är värdar.
- **Virtual Machines**
    - **Processor användning för virtuell dator** Visar en grafisk trend för processor användningen för virtuella datorer och en lista över virtuella datorer, baserat på den valda tids perioden. Hovra över linje diagrammet om du vill visa information om en viss tidpunkt för de tre översta virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggs ökningen. Klicka på namnet på den virtuella datorn för att öppna loggs ökning och Visa information om sammanställd CPU-räknare för den virtuella datorn.
    - **Minnes användning för virtuell dator** Visar en grafisk trend för minnes användningen av virtuella datorer och en lista över virtuella datorer, baserat på den valda tids perioden. Hovra över linje diagrammet om du vill visa information om en viss tidpunkt för de tre översta virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggs ökningen. Klicka på namnet på den virtuella datorn för att öppna loggs ökning och Visa information om sammanställd minnes räknare för den virtuella datorn.
    - **VM totalt disk-IOPS** Visar en grafisk trend för den totala disk IOPS för virtuella datorer och en lista över virtuella datorer med IOPS för var och en, baserat på den valda tids perioden. Hovra över linje diagrammet om du vill visa information om en viss tidpunkt för de tre översta virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggs ökningen. Klicka på namnet på den virtuella datorn för att öppna loggs ökning och Visa information om räknare för aggregerad disk-IOPS för den virtuella datorn.
    - **Disk data flöde för total virtuell dator** Visar en grafisk trend för det totala disk data flödet för virtuella datorer och en lista över virtuella datorer med det totala disk data flödet för varje, baserat på den valda tids perioden. Hovra över linje diagrammet om du vill visa information om en viss tidpunkt för de tre översta virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggs ökningen. Klicka på namnet på den virtuella datorn för att öppna loggs ökning och Visa sammanställd information om totala disk data flöde för den virtuella datorn.
- **Klusterdelade volymer**
    - **Totalt data flöde** Visar summan av både läsningar och skrivningar på klusterdelade volymer.
    - **Totalt antal IOPS** Visar summan av in-/utdata-åtgärder per sekund på klusterdelade volymer.
    - **Total svars tid** Visar den totala svars tiden för klusterdelade volymer.
- **Värd densitet** Den övre panelen visar det totala antalet värdar och virtuella datorer som är tillgängliga för lösningen. Klicka på den övre panelen om du vill visa mer information i loggs ökningen. Visar även alla värdar och antalet virtuella datorer som finns. Klicka på en värd för att öka detalj nivån i VM-resultatet i en loggs ökning.


![bladet värd för instrument panel](./media/capacity-performance/dashboard-hosts.png)

![bladet instrument panel för virtuella datorer](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Utvärdera prestanda

Miljöer för produktions data behandling skiljer sig kraftigt från en organisation till en annan. Arbets belastningar för kapacitet och prestanda kan också vara beroende av hur dina virtuella datorer körs och vad du vill ha normalt. Vissa procedurer som hjälper dig att mäta prestanda gäller förmodligen inte för din miljö. Därför är mer generaliserad vägledning bättre att hjälpa dig. Microsoft publicerar en rad olika vägledningar om vägledning som hjälper dig att mäta prestanda.

För att sammanfatta samlar lösningen in kapacitets-och prestanda data från en mängd olika källor, inklusive prestanda räknare. Använd dessa kapacitets-och prestanda data som presenteras på olika ytor i lösningen och jämför resultaten med dem i artikeln [Mät prestanda i Hyper-V](https://www.microsoft.com/en-us/download/details.aspx?id=56495) . Även om artikeln publicerades någon tid sedan, är måtten, övervägandena och rikt linjerna fortfarande giltiga. Artikeln innehåller länkar till andra användbara resurser.


## <a name="sample-log-searches"></a>Exempel på loggsökningar

Följande tabell innehåller exempel på loggs ökningar för kapacitets-och prestanda data som samlas in och beräknas av den här lösningen.


| Fråga | Beskrivning |
|:--- |:--- |
| Alla konfigurationer av värd minne | Perf &#124; WHERE ObjectName = = "Kapacitet och prestanda" och CounterName = = "Host Assigned Memory MB" &#124; sammanfatta MB = AVG (CounterValue) efter InstanceName |
| Alla konfigurationer för virtuella datorer | Perf &#124; WHERE ObjectName = = "Kapacitet och prestanda" och CounterName = = "VM Assigned Memory MB" &#124; sammanfatta MB = AVG (CounterValue) efter InstanceName |
| Nedbrytning av total disk-IOPS i alla virtuella datorer | Perf &#124; WHERE ObjectName = = "Kapacitet och prestanda" och (CounterName = = "VHD-läsningar/s" eller CounterName = = "VHD-skrivningar/s") &#124; sammanfatta AggregatedValue = AVG (CounterValue) per bin (TimeGenerated, 1H), CounterName, InstanceName |
| Analys av totalt disk data flöde på alla virtuella datorer | Perf &#124; WHERE ObjectName = = "Kapacitet och prestanda" och (CounterName = = "VHD Read MB/s" eller CounterName = = "VHD Write MB/s") &#124; sammanfatta AggregatedValue = AVG (CounterValue) per bin (TimeGenerated, 1H), CounterName, InstanceName |
| Nedbrytning av total IOPS i alla CSV: er | Perf &#124; WHERE ObjectName = = "Kapacitet och prestanda" och (CounterName = = "CSV-läsningar/s" eller CounterName = = "CSV-skrivningar/s") &#124; sammanfatta AggregatedValue = AVG (CounterValue) per bin (TimeGenerated, 1H), CounterName, InstanceName |
| Analys av totalt genomflöde i alla CSV: er | Perf &#124; WHERE ObjectName = = "Kapacitet och prestanda" och (CounterName = = "CSV-läsningar/s" eller CounterName = = "CSV-skrivningar/s") &#124; sammanfatta AggregatedValue = AVG (CounterValue) per bin (TimeGenerated, 1H), CounterName, InstanceName |
| Nedbrytning av total svars tid för alla CSV: er | Perf &#124; WHERE ObjectName = = "Kapacitet och prestanda" och (CounterName = = "CSV Read Latenion" eller CounterName = = "CSV Skriv fördröjning") &#124; sammanfatta AggregatedValue = AVG (CounterValue) per bin (TimeGenerated, 1H), CounterName, InstanceName |


## <a name="next-steps"></a>Nästa steg
* Använd [loggs ökningar i Log Analytics](../logs/log-query-overview.md) om du vill visa detaljerade kapacitet och prestanda data.

