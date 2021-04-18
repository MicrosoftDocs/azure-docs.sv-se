---
title: Ansluta Aliaside kAudit-data till Azure Sentinel| Microsoft Docs
description: Lär dig hur du ansluter Alias kAudit-data till Azure Sentinel.
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
ms.openlocfilehash: b146e228de13109975a76b0e4c6c9fd183fd362d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600412"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Anslut Ditt Aliasid kAudit till Azure Sentinel

> [!IMPORTANT]
> Dataanslutningsappen Alias kAudit i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Med Alias kAudit](https://www.alcide.io/kaudit-K8s-forensics/) kan du identifiera avvikande Kubernetes-beteenden och fokusera på Kubernetes-överträdelser och incidenter samtidigt som du minskar identifieringstiden. I den här artikeln förklaras hur du ansluter din Alide kAudit-lösning till Azure Sentinel. Med dataanslutningsappen Alias kAudit kan du enkelt föra in dina kAudit-loggdata i Azure Sentinel, så att du kan visa dem i arbetsböcker, använda dem för att skapa anpassade aviseringar och lägga till dem för att förbättra undersökningen. Integrering mellan Aliaside kAudit och Azure Sentinel använder REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbetsyta som du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs- och skrivbehörighet på Azure Sentinel arbetsytan.

- Du måste ha läsbehörighet till delade nycklar för arbetsytan.

## <a name="configure-and-connect-alcide-kaudit"></a>Konfigurera och ansluta Alcide kAudit

Alias kAudit kan exportera loggar direkt till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du **på Datakopplingar** på navigeringsmenyn.

1. Välj **Alcide kAudit** från galleriet och klicka sedan på **knappen Öppna anslutningssidan.**

1. Följ de stegvisa anvisningarna i [installationsguiden för Aliaside kAudit.](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit)

1. När du tillfrågas om arbetsyte-ID och primärnyckel kan du kopiera dem från sidan för anslutningsappen Förkaudit-data.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="Arbetsyte-ID och primärnyckel":::

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **Loggar** under följande datatyper i **CustomLogs:**

- **alcide_kaudit_detections_1_CL** – Alias kAudit-identifieringar 
- **alcide_kaudit_activity_1_CL** – Alias kAudit-aktivitetsloggar
- **alcide_kaudit_selections_count_1_CL** – AntalkAudit-aktiviteter för Alias
- **alcide_kaudit_selections_details_1_CL** – Information om Aktivitetsdetaljer för Aliasid kAudit

Om du vill använda det relevanta schemat i Loggar för Xmlide kAudit söker du efter de datatyper som nämns ovan.

Det kan ta upp till 20 minuter innan loggarna visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Aliaside kAudit till Azure Sentinel. Om du vill dra full nytta av funktionerna som är inbyggda i den här dataanslutningen klickar du på **fliken Nästa** steg på sidan för dataanslutning. Där hittar du några färdiga exempelfrågor så att du kan komma igång med att hitta användbar information.

Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur [du får insyn i dina data och potentiella hot.](quickstart-get-visibility.md)
- Kom igång [med att identifiera hot Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.
