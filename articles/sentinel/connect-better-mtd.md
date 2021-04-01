---
title: Ansluta bättre skydd mot mobila hot (MTD) till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder den bättre data anslutningen för MTD-data (Mobile Threat försvar) för att hämta MTD-loggar till Azure Sentinel. Visa MTD-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98541591"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>Anslut ditt bättre skydd mot mobila hot (MTD) till Azure Sentinel

> [!IMPORTANT]
> Den bättre MTD-anslutaren (Mobile Threat försvar) är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Med den bättre MTD-anslutningen (Mobile Threat försvar) kan du enkelt ansluta alla dina bättre MTD-säkerhetslösnings loggar med din Azure Sentinel, Visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan bättre skydd mot mobila hot och Azure Sentinel använder REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>Konfigurera och Anslut bättre skydd mot mobila hot

BÄTTRE MTD kan integrera och exportera loggar direkt till Azure Sentinel.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. I galleriet **data anslutningar** väljer du **bättre MTD (Mobile Threat försvar) (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ stegen på anslutnings sidan och på [den här sidan från den bättre MTD-dokumentationen](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) för att slutföra integrationen på en bättre MTD-konsol.

    När du uppmanas att ange **arbetsyte-ID** och **primär nyckel** värden kopierar du dem från sidan Azure Sentinel Connector och klistrar in dem i den bättre MTD-konfigurationen.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{Arbetsyte-ID och primär nyckel}":::

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **CustomLogs** i en eller flera av följande tabeller:
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

Om du vill fråga de bättre MTD-loggarna i analys regler, jakt frågor eller var som helst i Azure Sentinel, anger du ett av ovanstående tabell namn överst i frågefönstret.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter bättre MTD (Mobile Threat försvar) till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
