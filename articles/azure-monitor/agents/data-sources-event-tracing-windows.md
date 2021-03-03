---
title: Samla in ETW (Event Tracing for Windows)-händelser (ETW) för analys Azure Monitor loggar
description: Lär dig hur du samlar in ETW (Event Tracing for Windows) (ETW) för analys i Azure Monitor loggar.
services: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: d0ded409d76d0b26a76aebb47b8de8f6143ceba5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719907"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Samla in ETW (Event Tracing for Windows)-händelser (ETW) för analys Azure Monitor loggar

*ETW (Event tracing for Windows) (ETW)* tillhandahåller en mekanism för instrumentering av program i användarläge och driv rutiner för kernel-läge. Log Analytics Agent används för att [samla in Windows-händelser](./data-sources-windows-events.md) som skrivits till de administrativa och operativa [ETW-kanalerna](/windows/win32/wes/eventmanifestschema-channeltype-complextype). Det är dock ibland nödvändigt att samla in och analysera andra händelser, till exempel de som skrivs till den analytiska kanalen.  

## <a name="event-flow"></a>Händelse flöde

För att kunna samla in [manifestbaserade ETW-händelser](/windows/win32/etw/about-event-tracing#types-of-providers) för analys i Azure Monitor loggar måste du använda [Azure Diagnostics Extension](./diagnostics-extension-overview.md) för Windows (wad). I det här scenariot fungerar diagnostikprogrammet som ETW-konsument, och skriver händelser till Azure Storage (tabeller) som en mellanliggande lagrings plats. Här kommer den att lagras i en tabell med namnet **WADETWEventTable**. Log Analytics samlar sedan in tabell data från Azure Storage och presenterar dem som en tabell med namnet **ETWEvent**.

![Händelse flöde](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>Konfigurera ETW-logg samling

### <a name="step-1-locate-the-correct-etw-provider"></a>Steg 1: hitta rätt ETW-Provider

Använd något av följande kommandon för att räkna upp ETW-providrar på ett käll-Windows-system.

Kommando rad:

```
logman query providers
```

PowerShell:
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
Alternativt kan du välja att skicka PowerShell-utdata till Out-Gridview för att under lätta navigeringen.

Registrera namn och GUID för ETW-providern som motsvarar den analytiska eller fel söknings logg som visas i Loggboken, eller till den modul som du vill samla in händelse data för.

### <a name="step-2-diagnostics-extension"></a>Steg 2: tillägg för diagnostik

Se till att *Windows diagnostik-tillägget* är [installerat](./diagnostics-extension-windows-install.md#install-with-azure-portal) på alla käll system.

### <a name="step-3-configure-etw-log-collection"></a>Steg 3: Konfigurera ETW-logg samling

1. Navigera till bladet **Inställningar för diagnostik** på den virtuella datorn

2. Välj fliken **loggar**

3. Rulla nedåt och aktivera alternativet **händelse spårning för Windows (ETW)** ![ skärm bild av diagnostikinställningar](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. Ange providerns GUID eller leverantörs klass baserat på den provider som du konfigurerar samling för

5. Ange lämplig [**loggnings nivå**](/windows/win32/etw/configuring-and-starting-an-event-tracing-session)

6. Klicka på ellipsen bredvid den angivna providern och klicka på **Konfigurera**

7. Se till att **standard mål tabellen** är inställd på **etweventtable**

8. Ange ett [**nyckelords filter**](/windows/win32/wes/defining-keywords-used-to-classify-types-of-events) om det behövs

9. Spara inställningarna för Provider och logg

När du har genererat händelser bör du börja se vilka ETW-händelser som visas i tabellen **WADetweventtable** i Azure Storage. Du kan använda Azure Storage Explorer för att bekräfta detta.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>Steg 4: Konfigurera Log Analytics lagrings konto samling

Följ [dessa anvisningar](/azure/azure-monitor/agents/diagnostics-extension-logs#collect-logs-from-azure-storage) för att samla in loggarna från Azure Storage. När den har kon figurer ATS ska ETW-händelsens data visas i Log Analytics under tabellen **ETWEvent** .

## <a name="next-steps"></a>Nästa steg
- Använd [anpassade fält](../logs/custom-fields.md) för att skapa en struktur i dina ETW-händelser
- Lär dig mer om [logg frågor](../logs/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.