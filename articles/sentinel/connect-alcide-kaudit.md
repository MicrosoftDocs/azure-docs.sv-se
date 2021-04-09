---
title: Anslut Alcide kAudit-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Alcide kAudit-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: 6d1314b79f09f1bed2de5d6964b0a6224b5962ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096879"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Anslut din Alcide-kAudit till Azure Sentinel

> [!IMPORTANT]
> Alcide kAudit data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) hjälper dig att identifiera avvikande Kubernetes beteenden och fokusera på Kubernetes överträdelser och incidenter samtidigt som du minskar identifierings tiden. Den här artikeln förklarar hur du ansluter din Alcide kAudit-lösning till Azure Sentinel. Med Alcide kAudit data Connector kan du enkelt ta med dina kAudit-loggdata till Azure Sentinel, så att du kan visa den i arbets böcker, använda den för att skapa anpassade aviseringar och införliva den för att förbättra undersökningen. Integreringen mellan Alcide kAudit och Azure Sentinel använder REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha Läs behörighet till delade nycklar för arbets ytan.

## <a name="configure-and-connect-alcide-kaudit"></a>Konfigurera och ansluta Alcide-kAudit

Alcide kAudit kan exportera loggar direkt till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** på navigerings menyn.

1. Välj **Alcide kAudit** från galleriet och klicka sedan på knappen **Öppna kopplings sida** .

1. Följ de stegvisa anvisningarna i [installations guiden för Alcide-kAudit](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf).

1. När du uppmanas till arbetsyte-ID: t och den primära nyckeln kan du kopiera dem från Alcide kAudit data Connector-sidan.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="Arbetsyte-ID och primär nyckel":::

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under följande data typer i **CustomLogs**:

- **alcide_kaudit_detections_1_CL** -Alcide kaudit-identifieringar 
- **alcide_kaudit_activity_1_CL** -Alcide kaudit aktivitets loggar
- antal **alcide_kaudit_selections_count_1_CL** -Alcide kaudit-aktiviteter
- information om **alcide_kaudit_selections_details_1_CL** -Alcide kaudit-aktivitet

Om du vill använda det relevanta schemat i loggar för Alcide-kAudit kan du söka efter de data typer som anges ovan.

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Alcide-kAudit till Azure Sentinel. Om du vill dra full nytta av de funktioner som är inbyggda i denna data anslutning klickar du på fliken **Nästa steg** på data anslutnings sidan. Där hittar du några färdiga exempel frågor så att du kan komma igång med att hitta användbar information.

Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
