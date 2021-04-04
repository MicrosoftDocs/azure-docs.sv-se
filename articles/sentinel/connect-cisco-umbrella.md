---
title: Anslut Cisco-paraply till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Ciscos parasoll data Connector för att hämta paraply data till Azure Sentinel. Visa Parasolls data i arbets böcker, skapa aviseringar och förbättra undersökningen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 22796134dae5c345e3f915e47bc1300affb9f60e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99566947"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>Anslut ditt Cisco-paraply till Azure Sentinel

> [!IMPORTANT]
> Cisco parasoll-anslutningsprogrammet är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Med Cisco parasoll-anslutningsprogrammet kan du enkelt ansluta alla dina säkerhets lösningar för Cisco parasoll med din Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan Cisco-paraply och Azure Sentinel använder REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-cisco-umbrella"></a>Konfigurera och Anslut Cisco paraply

Cisco paraply kan integrera och exportera loggar direkt till Azure Sentinel.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. Från **data kopplings** galleriet väljer du **Cisco paraply (för hands version)** och sedan **Öppna kopplings sida**.

1. Följ stegen som beskrivs i **konfigurations** avsnittet på kopplings sidan.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **CustomLogs** i en eller flera av följande tabeller:
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Cisco parasoll-data till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
