---
title: Anslut AI-Vectra identifiera data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter AI-Vectra identifiera data till Azure Sentinel.
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
ms.openlocfilehash: c57f4a49ac89f1347fc88f8bacddce3abab7e44e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87038230"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>Anslut AI-Vectra identifiera till Azure Sentinel

> [!IMPORTANT]
> AI-Vectra identifiera data koppling i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln förklarar hur du ansluter din [AI-Vectra identifiera](https://www.vectra.ai/product/cognito-detect) installationen till Azure Sentinel. Med AI-Vectra identifiera data Connector kan du enkelt se till att dina AI-Vectra identifierar data i Azure Sentinel, så att du kan visa dem i arbets böcker, använda dem för att skapa anpassade aviseringar och införliva dem för att förbättra undersökningen.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>Konfigurera din AI-Vectra identifiera apparat för att skicka CEF-meddelanden  

Konfigurera AI-Vectra identifiera för att vidarebefordra CEF-formaterade syslog-meddelanden till din Log Analytics-arbetsyta via den syslog-vidarebefordrare som du konfigurerade i [steg 1: Distribuera logg vidarebefordraren](connect-cef-agent.md).

1. I Vectra-gränssnittet navigerar du till inställningar > meddelanden och väljer Redigera syslog-konfiguration. Följ anvisningarna nedan för att konfigurera anslutningen:

    - Lägg till ett nytt mål (hostname för [logg vidarebefordraren](connect-cef-agent.md))
    - Ställ in porten som **514**
    - Ange protokollet som **UDP**
    - Ange formatet till **CEF**
    - Ange logg typer (Välj alla tillgängliga logg typer)
    - Klicka på **Spara**

2. Du kan klicka på knappen **testa** för att framtvinga sändningen av vissa test händelser till logg vidarebefordraren.

3. Om du vill använda det relevanta schemat i Log Analytics för AI-Vectra identifiera händelser söker du efter **CommonSecurityLog**.

4. Fortsätt till [steg 3: verifiera anslutningen](connect-cef-verify.md).

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter AI-Vectra identifiera enheter till Azure Sentinel. Om du vill dra full nytta av de funktioner som är inbyggda i denna data anslutning klickar du på fliken **Nästa steg** på data anslutnings sidan. Där hittar du några färdiga exempel frågor så att du kan komma igång med att hitta användbar information.

Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
