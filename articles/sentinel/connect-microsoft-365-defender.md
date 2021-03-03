---
title: Anslut Microsoft 365 Defender-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du matar in incidenter, aviseringar och rå data från Microsoft 365 Defender till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 6500805a4dc7e26f5e1bc601df9ea78279ae17e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709350"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Anslut data från Microsoft 365 Defender till Azure Sentinel

> [!IMPORTANT]
>
> **Microsoft 365 Defender** kallades tidigare **Microsoft Threat Protection** eller **MTP**.
>
> **Microsoft Defender för slut punkten** kallades tidigare **Microsoft Defender Advanced Threat Protection** eller **MDATP**.
>
> Du kan se att de gamla namnen fortfarande används under en viss tids period.

## <a name="background"></a>Bakgrund

Med Azure Sentinels [Microsoft 365 Defender-anslutning (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) med incident integrering kan du strömma alla M365D-incidenter och aviseringar till Azure Sentinel och hålla de incidenter som synkroniseras mellan båda portalerna. M365D-incidenter innehåller alla aviseringar, entiteter och annan relevant information, och de hanteras av och grupperar aviseringar från M365D's komponent tjänster **Microsoft Defender för slut punkt**, **Microsoft Defender för identitet**, **Microsoft defender för Office 365** och **Microsoft Cloud App Security**.

Med anslutnings programmet kan du också strömma **avancerade jakt** händelser från Microsoft Defender för slut punkt till Azure Sentinel, så att du kan kopiera MDE-avancerade jakt frågor till Azure Sentinel, utöka Sentinel-aviseringar med MDE rå Event data för att ge ytterligare insikter och lagra loggarna med ökad kvarhållning i Log Analytics.

Mer information om incident integrering och insamling av avancerade jakt händelser finns i [Microsoft 365 Defender-integrering med Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

> [!IMPORTANT]
>
> Microsoft 365 Defender-anslutaren är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en giltig licens för Microsoft 365 Defender, enligt beskrivningen i [Microsoft 365 Defender-krav](/microsoft-365/security/mtp/prerequisites). 

- Du måste vara **Global administratör** eller en **säkerhets administratör** i Azure Active Directory.

## <a name="connect-to-microsoft-365-defender"></a>Anslut till Microsoft 365 Defender

1. I Azure Sentinel väljer du **data kopplingar**, väljer **Microsoft 365 Defender (förhands granskning)** från galleriet och väljer **Öppna kopplings sida**.

1. Under **konfiguration** i avsnittet **anslut incidenter & aviseringar** klickar du på knappen **Anslut incidenter & varningar** .

1. För att undvika dubbletter av incidenter rekommenderar vi att du markerar kryss rutan **Stäng av alla regler för att skapa Microsoft-incidenter för dessa produkter.**

    > [!NOTE]
    > När du aktiverar Microsoft 365 Defender-anslutningen ansluts alla M365D-komponenternas kopplingar (de som nämns i början av den här artikeln) automatiskt i bakgrunden. Innan du kan koppla bort en av komponent kopplingarna måste du först koppla från Microsoft 365 Defender-anslutningen.

1. Om du vill fråga M365 Defender incident data använder du följande instruktion i frågefönstret:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Om du vill samla in avancerade jakt händelser från Microsoft Defender för slut punkt kan följande typer av händelser samlas in från sina motsvarande avancerade jakt tabeller.

    1. Markera kryss rutorna för tabellerna med de händelse typer som du vill samla in:

       | Tabellnamn | Händelse typ |
       |-|-|
       | DeviceInfo | Dator information (inklusive OS-information) |
       | DeviceNetworkInfo | Nätverks egenskaper för datorer |
       | DeviceProcessEvents | Skapande av processer och relaterade händelser |
       | DeviceNetworkEvents | Nätverks anslutning och relaterade händelser |
       | DeviceFileEvents | Skapa, ändra och andra fil system händelser |
       | DeviceRegistryEvents | Skapa och ändra register poster |
       | DeviceLogonEvents | Inloggningar och andra autentiserings händelser |
       | DeviceImageLoadEvents | DLL inläsning av händelser |
       | DeviceEvents | Ytterligare händelse typer |
       | DeviceFileCertificateInfo | Certifikat information för signerade filer |
       |

    1. Klicka på **tillämpa ändringar**.

    1. Om du vill fråga avancerade jakt tabeller i Log Analytics anger du tabell namnet från listan ovan i frågefönstret.

## <a name="verify-data-ingestion"></a>Verifiera data inmatning

Data grafen på kopplings sidan visar att du matar in data. Du ser att det visar en rad var för incidenter, aviseringar och händelser och att händelse raden är en agg regering av händelse volymen i alla aktiverade tabeller. När du har aktiverat anslutnings tjänsten kan du använda följande KQL-frågor för att skapa mer detaljerade diagram.

Använd följande KQL-fråga för ett diagram över inkommande M365 Defender-incidenter:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Använd följande KQL-fråga för att generera en graf över händelse volymen för en enskild tabell (ändra *DeviceEvents* -tabellen till önskad tabell som du väljer):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

På fliken **Nästa steg** hittar du några användbara arbets böcker, exempel frågor och mallar för analys regler som har inkluderats. Du kan köra dem på platsen eller ändra och spara dem.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du integrerar Microsoft 365 Defender-incidenter och avancerade jakt händelse data från Microsoft Defender för slut punkt, till Azure Sentinel, med hjälp av Microsoft 365 Defender-anslutningen. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](./tutorial-detect-threats-built-in.md).
