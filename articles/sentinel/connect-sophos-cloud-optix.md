---
title: Anslut Sophos Cloud Optix-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Sophos Cloud Optix-anslutningsprogrammet för att hämta <PRODUCT NAME> loggar till Azure Sentinel. Visa <PRODUCT NAME> data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700935"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Anslut ditt Sophos Cloud-Optix till Azure Sentinel

> [!IMPORTANT]
> Sophos Cloud Optix-anslutningsprogrammet är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Med Sophos Cloud Optix Connector kan du enkelt ansluta alla dina Sophos Cloud Optix Security Solution-loggar med din Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen.  Den här funktionen ger dig mer information om din organisations position för moln säkerhet och efterlevnad och förbättrar dina funktioner för säkerhets åtgärder i molnet. Integreringen mellan Sophos Cloud Optix och Azure Sentinel använder REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-sophos-cloud-optix"></a>Konfigurera och ansluta Sophos Cloud Optix

Sophos Cloud Optix kan integrera och exportera loggar direkt till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **Sophos Cloud Optix (för hands version)**.

1. Välj **Öppna kopplings sida**.

1. Kopiera och spara **arbetsyte-ID** och **primär nyckel** från anslutnings sidan.

1. Följ anvisningarna från Sophos för att [integrera med Microsoft Azure Sentinel](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) (från det tredje steget).

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggar** under avsnittet **anpassade loggar** i tabellen *SophosCloudOptix_CL* .

Om du vill fråga Sophos Cloud Optix-data anger du `SophosCloudOptix_CL` i frågefönstret. Se **Nästa steg** -fliken på kopplings sidan och i [Sophos-dokumentationen](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html)för några användbara fråge exempel.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Sophos Cloud Optix till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
