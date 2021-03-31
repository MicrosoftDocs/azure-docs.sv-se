---
title: Anslut din Apache HTTP-server till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Apache HTTP server Connector för att hämta Apache-loggar till Azure Sentinel. Visa Apache-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 6a1a2a2a7dac961e49e6ced38803649ebf5ad523
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096862"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Anslut din Apache HTTP-server till Azure Sentinel

> [!IMPORTANT]
> Apache HTTP server Connector är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter din Apache HTTP-server till Azure Sentinel. Med Apache HTTP server Connector kan du enkelt mata in dina Apache HTTP-server-loggar i Azure Sentinel, så att du kan visa data i arbets böcker, fråga den för att skapa anpassade aviseringar och införliva den för att förbättra undersökningen. Integreringen mellan Apache HTTP server och Azure Sentinel använder lokal fil bearbetning av Log Analytics agenten.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha Skriv behörighet på Azure Sentinel-arbetsytan.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Konfigurera och integrera Apache HTTP server-loggar via Log Analytics agent

Konfigurera Apache HTTP-server för att skicka loggfiler till Azure-arbetsytan via Log Analytics-agenten.
Konfigurera Log Analytics agent för att läsa Apache HTTP server-loggfiler.

1. Följ anvisningarna på https://httpd.apache.org/docs/2.4/logs.html för att ställa in platsen för loggfiler i Apache HTTP server.

1. På navigerings menyn i Azure Sentinel väljer du **data kopplingar** och väljer sedan **Apache HTTP server (för hands version)**.

1. Välj **Öppna kopplings sida**.

1. Följ instruktionerna på sidan Apache HTTP-server.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under ApacheHTTPServer_CL.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Apache HTTP-server till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
