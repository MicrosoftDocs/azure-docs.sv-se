---
title: Övervaka hälso tillståndet för dina data anslutningar med den här Azure Sentinel-arbetsboken | Microsoft Docs
description: Använd arbets boken för hälso övervakning för att hålla reda på anslutningen och prestandan för dina data anslutningar.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94656998"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>Övervaka hälso tillståndet för dina data anslutningar med den här Azure Sentinel-arbetsboken

Med **arbets boken för hälso övervakning av data anslutningar** kan du hålla reda på dina data anslutningars hälsa, anslutning och prestanda från Azure Sentinel. Arbets boken innehåller ytterligare övervakare, identifierar avvikelser och ger insikter om arbets ytans status för data inmatning. Du kan använda arbets bokens logik för att övervaka allmän hälsa för inmatade data och för att bygga anpassade vyer och regelbaserade aviseringar.

## <a name="use-the-health-monitoring-workbook"></a>Använda arbets boken för hälso övervakning

1. Från Azure Sentinel-portalen väljer du **arbets böcker** på menyn **Threat Management** .

1. I galleriet för **arbets böcker** anger du *hälsan* i Sök fältet och väljer **data insamling hälso övervakning** från resultaten.

1. Välj **Visa mall** för att använda arbets boken som den är eller Välj **Spara** för att skapa en redigerbar kopia av arbets boken. När du har skapat kopian väljer du **Visa Sparad arbets bok**.

1. När du är i arbets boken väljer du först den **prenumeration** och **arbets yta** som du vill visa. definiera sedan **TimeRange** för att filtrera data efter dina behov. Använd alternativet **Visa hjälp** för att visa en förklaring av arbets boken på plats.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="Start sida för data Connector Health Monitoring-arbetsbok" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

Det finns tre avsnitt med flikar i den här arbets boken:

1. På fliken **Översikt** visas allmän status för data inmatning på den valda arbets ytan: volym mått, EPS-priser och tid då den senaste loggen togs emot.

1. På fliken **avvikelser för data insamling** kan du identifiera avvikelser i data insamlings processen, efter tabell och data källa. Varje flik visar avvikelser för en viss tabell (fliken **Allmänt** innehåller en samling tabeller). Avvikelserna beräknas med hjälp av funktionen **series_decompose_anomalies ()** som returnerar ett **avvikelse resultat**. [Läs mer om den här funktionen](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). Ange följande parametrar för funktionen för att utvärdera:

    - **AnomaliesTimeRange**: Den här tidväljaren gäller endast för vyn för datainsamlingsavvikelser.
    - **SampleInterval**: Det tidsintervall som data samplas inom. Avvikelsepoängen beräknas endast för det sista intervallets data.
    - **PositiveAlertThreshold**: Det här värdet definierar tröskelvärdet för positiv avvikelsepoäng. Det accepterar decimal värden.
    - **NegativeAlertThreshold**: Det här värdet definierar tröskelvärdet för negativ avvikelsepoäng. Det accepterar decimal värden.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="Sidan avvikelser vid övervakning av data kopplings arbets bok" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. På fliken **agent information** visas information om hälsan hos de log Analyticss agenter som är installerade på olika datorer, oavsett om Azure VM, annan virtuell dator i molnet, lokalt eller fysiskt. Du kan övervaka följande:

   - System plats

   - Pulsslags status och svars tid

   - Tillgängligt minne och disk utrymme

   - Agent åtgärder

    I det här avsnittet måste du välja den flik som beskriver dator miljön: Välj fliken **Azure-hanterade datorer** om du bara vill visa de Azure Arc-hanterade datorerna. Välj fliken **alla datorer** om du vill visa både hanterade och icke-Azure-datorer med den Log Analytics-agenten installerad.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="Sidan agent information om data Connector Health Monitoring arbets bok" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>Nästa steg
Lär dig hur du kan [publicera dina data till Azure Sentinel](quickstart-onboard.md), [ansluta data källor](connect-data-sources.md)och [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).