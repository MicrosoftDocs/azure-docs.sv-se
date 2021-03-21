---
title: Dator grupper i Azure Monitor logg frågor | Microsoft Docs
description: Med dator grupper i Azure Monitor kan du begränsa logg frågor till en viss uppsättning datorer.  I den här artikeln beskrivs de olika metoder som du kan använda för att skapa dator grupper och hur du använder dem i en logg fråga.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: d8702b498e08561175aa7ee975c7b6b46fdf1687
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031097"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Dator grupper i Azure Monitor logg frågor
Med dator grupper i Azure Monitor kan du begränsa [logg frågor](./log-query-overview.md) till en viss uppsättning datorer.  Varje grupp fylls med datorer antingen med en fråga som du definierar eller genom att importera grupper från olika källor.  När gruppen ingår i en logg fråga begränsas resultatet till poster som matchar datorerna i gruppen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Skapa en dator grupp
Du kan skapa en dator grupp i Azure Monitor med någon av metoderna i följande tabell.  Information om varje metod finns i avsnitten nedan. 

| Metod | Beskrivning |
|:--- |:--- |
| Logg fråga |Skapa en logg fråga som returnerar en lista med datorer. |
| Loggsöknings-API |Använd API för loggs ökning för att program mässigt skapa en dator grupp baserat på resultatet av en logg fråga. |
| Active Directory |Sök automatiskt efter grupp medlemskap för alla agent datorer som är medlemmar i en Active Directory domän och skapa en grupp i Azure Monitor för varje säkerhets grupp. (Endast Windows-datorer)|
| Configuration Manager | Importera samlingar från Microsoft Endpoint Configuration Manager och skapa en grupp i Azure Monitor för var och en. |
| Windows Server Update Services |Skanna automatiskt WSUS-servrar eller klienter för mål grupper och skapa en grupp i Azure Monitor för var och en. |

### <a name="log-query"></a>Logg fråga
Dator grupper som skapas från en logg fråga innehåller alla datorer som returneras av en fråga som du definierar.  Den här frågan körs varje gång dator gruppen används så att eventuella ändringar sedan gruppen skapades visas.  

Du kan använda valfri fråga för en dator grupp, men den måste returnera en distinkt uppsättning datorer med hjälp av `distinct Computer` .  Följande är en typisk exempel fråga som du kan använda som en dator grupp.

```kusto
Heartbeat | where Computer contains "srv" | distinct Computer
```

Använd följande procedur för att skapa en dator grupp från en loggs ökning i Azure Portal.

1. Klicka på **loggar** på **Azure Monitor** -menyn i Azure Portal.
1. Skapa och kör en fråga som returnerar de datorer som du vill ha i gruppen.
1. Klicka på **Spara** längst upp på skärmen.
1. Ändra **Spara som** till **funktion** och välj **Spara den här frågan som en dator grupp**.
1. Ange värden för varje egenskap för dator gruppen som beskrivs i tabellen och klicka på **Spara**.

I följande tabell beskrivs de egenskaper som definierar en dator grupp.

| Egenskap | Beskrivning |
|:---|:---|
| Name   | Namnet på frågan som ska visas i portalen. |
| Funktions Ali Aset | Ett unikt alias som används för att identifiera dator gruppen i en fråga. |
| Kategori       | Kategori för att organisera frågorna i portalen. |


### <a name="active-directory"></a>Active Directory
När du konfigurerar Azure Monitor att importera Active Directory grupp medlemskap analyseras grupp medlemskapet för alla Windows-domänanslutna datorer med Log Analytics agenten.  En dator grupp skapas i Azure Monitor för varje säkerhets grupp i Active Directory, och varje Windows-dator läggs till i de dator grupper som motsvarar de säkerhets grupper som de är medlemmar i.  Detta medlemskap uppdateras kontinuerligt var fjärde timme.  

> [!NOTE]
> Importerade Active Directory grupper innehåller bara Windows-datorer.

Du konfigurerar Azure Monitor att importera Active Directory säkerhets grupper från **Avancerade inställningar** i din Log Analytics arbets yta i Azure Portal.  Välj **dator grupper** **Active Directory** och **importera Active Directory grupp medlemskap från datorer**.  Det krävs ingen ytterligare konfiguration.

![Dator grupper från Active Directory](media/computer-groups/configure-activedirectory.png)

När grupper har importer ATS visar menyn hur många datorer med grupp medlemskap som har identifierats och hur många grupper som importer ATS.  Du kan klicka på någon av dessa länkar om du vill returnera **ComputerGroup** -posterna med den här informationen.

### <a name="windows-server-update-service"></a>Windows Server Update Service
När du konfigurerar Azure Monitor för att importera WSUS-gruppmedlemskap analyseras mål grupps medlemskapet för alla datorer med Log Analytics agenten.  Om du använder mål på klient sidan, har alla datorer som är anslutna till Azure Monitor och ingår i alla WSUS-målfält importerade grupp medlemskap för att Azure Monitor. Om du använder riktad på Server sidan bör Log Analytics Agent installeras på WSUS-servern för att grupp medlemskaps informationen ska importeras till Azure Monitor.  Detta medlemskap uppdateras kontinuerligt var fjärde timme. 

Du konfigurerar Azure Monitor att importera WSUS-grupper från **Avancerade inställningar** i Log Analytics-arbetsytan i Azure Portal.  Välj **dator grupper**, **WSUS** och importera sedan **WSUS-gruppmedlemskap**.  Det krävs ingen ytterligare konfiguration.

![Dator grupper från WSUS](media/computer-groups/configure-wsus.png)

När grupper har importer ATS visar menyn hur många datorer med grupp medlemskap som har identifierats och hur många grupper som importer ATS.  Du kan klicka på någon av dessa länkar om du vill returnera **ComputerGroup** -posterna med den här informationen.

### <a name="configuration-manager"></a>Configuration Manager
När du konfigurerar Azure Monitor för att importera Configuration Manager samlings medlemskap skapas en dator grupp för varje samling.  Information om samlings medlemskap hämtas var 3: e timme för att dator grupperna ska vara aktuella. 

Innan du kan importera Configuration Manager samlingar måste du [ansluta Configuration Manager till Azure Monitor](collect-sccm.md).  

![Dator grupper från SCCM](media/computer-groups/configure-sccm.png)

När samlingar har importer ATS visar menyn antalet datorer där grupp medlemskap har identifierats och hur många grupper som importeras.  Du kan klicka på någon av dessa länkar om du vill returnera **ComputerGroup** -posterna med den här informationen.

## <a name="managing-computer-groups"></a>Hantera dator grupper
Du kan visa dator grupper som har skapats från en logg fråga eller API: et för loggs ökning från **Avancerade inställningar** i Log Analytics arbets ytan i Azure Portal.  Välj **dator grupper** och sedan **sparade grupper**.  

Klicka på **x** i kolumnen **ta bort** om du vill ta bort dator gruppen.  Klicka på ikonen **Visa medlemmar** för en grupp för att köra gruppens loggs ökning som returnerar medlemmarna.  Du kan inte ändra en dator grupp utan i stället måste ta bort och sedan återskapa den med de ändrade inställningarna.

![Sparade dator grupper](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Använda en dator grupp i en logg fråga
Du använder en dator grupp som skapats från en logg fråga i en fråga genom att behandla dess alias som en funktion, vanligt vis med följande syntax:

```kusto
Table | where Computer in (ComputerGroup)
```

Du kan till exempel använda följande för att returnera UpdateSummary-poster för endast datorer i en dator grupp med namnet mycomputergroup.

```kusto
UpdateSummary | where Computer in (mycomputergroup)
```

Importerade dator grupper och de datorer som ingår lagras i tabellen **ComputerGroup** .  Följande fråga skulle till exempel returnera en lista över datorer i gruppen domän datorer från Active Directory. 

```kusto
ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer
```

Följande fråga returnerar UpdateSummary-poster för endast datorer på domän datorer.

```kusto
let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
```

## <a name="computer-group-records"></a>Dator grupps poster
En post skapas i arbets ytan Log Analytics för varje dator grupp medlemskap som skapats från Active Directory eller WSUS.  Dessa poster har en typ av **ComputerGroup** och har egenskaperna i följande tabell.  Poster skapas inte för dator grupper baserat på logg frågor.

| Egenskap | Beskrivning |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Namn på medlems dator. |
| `Group` |Gruppens namn. |
| `GroupFullName` |Fullständig sökväg till gruppen inklusive källa och käll namn. |
| `GroupSource` |Källan som gruppen samlades in från. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Namnet på den källa som gruppen samlades in från.  För Active Directory är detta domän namnet. |
| `ManagementGroupName` |Namnet på hanteringsgruppen för SCOM-agenter.  För andra agenter är detta AOI-\<workspace ID\> |
| `TimeGenerated` |Datum och tid då dator gruppen skapades eller uppdaterades. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logg frågor](./log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.