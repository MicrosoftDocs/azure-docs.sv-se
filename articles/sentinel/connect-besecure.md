---
title: Ansluta bortom säkerhets säkra data till Azure Sentinel | Microsoft Docs
description: Lär dig mer om hur du använder säkerhets anslutningen för att skydda data på ett säkert sätt för att hämta säkra loggar till Azure Sentinel. Visa säkra data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.openlocfilehash: 313f201aeabd470850b27d979dc5253f80e82a55
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541178"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Anslut din säkerhets efter säkerhet till Azure Sentinel

> [!IMPORTANT]
> Den säkerhetsrelaterade säkerhets kopplingen är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Med hjälp av säkerhets säkra anslutnings-och säkerhets anslutningar kan du enkelt ansluta alla dina säkra säkerhets lösnings loggar med din Azure Sentinel, Visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan säkert och Azure Sentinel använder REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-besecure"></a>Konfigurera och anslut säkert

SÄKRA kan integreras med och exportera loggar direkt till Azure Sentinel.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. Från **data kopplings** galleriet väljer du **bortom säkerhet säker (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ stegen nedan för att konfigurera din säkra lösning för att skicka ut genomsöknings resultat, genomsöknings status och gransknings spårnings loggar till Azure Sentinel.

    **Öppna integrations menyn:**
    1. Klicka på meny alternativet Mer

    1. Välj server

    1. Välj integrering

    1. Aktivera Azure Sentinel

    **Ange säkra med Azure Sentinel-inställningar:**

      Kopiera *arbetsyte-ID: t* och *primär nyckel* värden från sidan Azure Sentinel Connector, klistra in dem i den säkra konfigurationen och klicka på **ändra**.
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{Arbetsyte-ID och primär nyckel}":::

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **CustomLogs** i en eller flera av följande tabeller:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Om du vill fråga efter säkra loggar i analys regler, jakt frågor, undersökningar eller var som helst i Azure Sentinel, anger du ett av ovanstående tabell namn överst i frågefönstret.

## <a name="validate-connectivity"></a>Verifiera anslutning
Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter säkert till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
