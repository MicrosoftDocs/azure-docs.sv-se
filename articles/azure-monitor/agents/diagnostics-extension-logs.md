---
title: Använd Blob Storage för IIS och tabell lagring för händelser i Azure Monitor | Microsoft Docs
description: Azure Monitor kan läsa loggar för Azure-tjänster som skriver diagnostik till Table Storage eller IIS-loggar som skrivs till Blob Storage.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: deb6b5f3718c1a7c84e3591bf9abcceb72b785da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054976"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Samla in data från Azure Diagnostics-tillägget till Azure Monitor loggar
Tillägget Azure Diagnostics är en [agent i Azure Monitor](../agents/agents-overview.md) som samlar in övervaknings data från gäst operativ systemet i Azure Compute-resurser, inklusive virtuella datorer. Den här artikeln beskriver hur du samlar in data som samlas in av diagnostikprogrammet från Azure Storage till Azure Monitor loggar.

> [!NOTE]
> Log Analytics agenten i Azure Monitor är vanligt vis den bästa metoden för att samla in data från gäst operativ systemet till Azure Monitor loggar. En detaljerad jämförelse av agenterna finns i [Översikt över Azure Monitors agenter](../agents/agents-overview.md) .

## <a name="supported-data-types"></a>Datatyper som stöds
Azure Diagnostics-tillägget lagrar data i ett Azure Storage-konto. För att Azure Monitor loggar för att samla in dessa data måste de finnas på följande platser:

| Loggtyp | Resurstyp | Location |
| --- | --- | --- |
| IIS-loggar |Virtual Machines <br> Webb roller <br> Arbets roller |wad-IIS-loggfiler (Blob Storage) |
| Syslog |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Service Fabric operativa händelser |Service Fabric noder |WADServiceFabricSystemEventTable |
| Service Fabric pålitliga aktörs händelser |Service Fabric noder |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable service Events |Service Fabric noder |WADServiceFabricReliableServiceEventTable |
| Händelse loggar i Windows |Service Fabric noder <br> Virtual Machines <br> Webb roller <br> Arbets roller |WADWindowsEventLogsTable (Table Storage) |
| Windows ETW-loggar |Service Fabric noder <br> Virtual Machines <br> Webb roller <br> Arbets roller |WADETWEventTable (Table Storage) |

## <a name="data-types-not-supported"></a>Data typer stöds inte

- Prestanda data från gäst operativ systemet
- IIS loggar från Azure Websites


## <a name="enable-azure-diagnostics-extension"></a>Aktivera Azure Diagnostics-tillägg
Se [Installera och konfigurera Windows Azure Diagnostics Extension (wad)](../agents/diagnostics-extension-windows-install.md) eller [Använd Linux-tillägg för att övervaka mått och loggar](../../virtual-machines/extensions/diagnostics-linux.md) för information om hur du installerar och konfigurerar tillägget diagnostik. Detta gör att du kan ange lagrings kontot och konfigurera insamling av de data som du vill vidarebefordra till Azure Monitor loggar.


## <a name="collect-logs-from-azure-storage"></a>Samla in loggar från Azure Storage
Använd följande procedur för att aktivera insamling av diagnostiska tilläggs data från ett Azure Storage-konto:

1. I Azure Portal går du till **Log Analytics arbets ytor** och väljer din arbets yta.
1. Klicka på **lagrings konto loggar** i avsnittet **data källor för arbets yta** på menyn.
2. Klicka på  **Lägg till**.
3. Välj det **lagrings konto** som innehåller de data som ska samlas in.
4. Välj den **datatyp som du vill** samla in.
5. Värdet för källan fylls i automatiskt baserat på data typen.
6. Spara ändringarna genom att klicka på **OK**.
7. Upprepa för ytterligare data typer.

På cirka 30 minuter kan du se data från lagrings kontot i Log Analytics-arbetsytan. Du kan bara se data som skrivs till lagring när konfigurationen har tillämpats. Arbets ytan läser inte befintliga data från lagrings kontot.

> [!NOTE]
> Portalen verifierar inte att källan finns i lagrings kontot eller om nya data skrivs.



## <a name="next-steps"></a>Nästa steg

* [Samla in loggar och mät värden för Azure-tjänster](../essentials/resource-logs.md#send-to-log-analytics-workspace) för Azure-tjänster som stöds.
* [Aktivera lösningar](../insights/solutions.md) för att ge inblick i data.
* [Använd Sök frågor](../logs/log-query-overview.md) för att analysera data.