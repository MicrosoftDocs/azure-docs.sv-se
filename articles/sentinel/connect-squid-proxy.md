---
title: Anslut squid-proxy-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder squid proxy data Connector för att hämta squid proxy-loggar till Azure Sentinel. Visa squid-proxy-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: eec88bf85f1b7a2ec8db2bf23c43629d84cc5106
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100090453"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>Anslut din squid-proxy till Azure Sentinel

> [!IMPORTANT]
> Squid proxy Connector är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter squid-proxyservern till Azure Sentinel. Med squid proxy data Connector kan du enkelt ansluta dina squid-loggar med Azure Sentinel, så att du kan visa data i arbets böcker, använda dem för att skapa anpassade aviseringar och införliva dem för att förbättra undersökningen. Integreringen mellan squid proxy och Azure Sentinel använder lokal fil bearbetning av Log Analytics agenten.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

## <a name="forward-squid-proxy-logs-to-the-log-analytics-agent"></a>Vidarebefordra squid proxy-loggar till Log Analytics agent  

Konfigurera squid proxy för att skicka loggfiler till Azure-arbetsytan via Log Analytics-agenten.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. I galleriet **data anslutningar** väljer du **squid proxy (för hands version)** och öppnar sedan **kopplings sidan**.

1. Följ instruktionerna på sidan **squid proxy** Connector:

    1. Installera och publicera agenten för Linux

        - Välj en virtuell Azure Linux-dator eller en icke-Azure Linux-dator (fysisk eller virtuell).

    1. Konfigurera loggarna som ska samlas in

        - I avancerade inställningar för arbets ytan lägger du till en anpassad loggtyp, laddar upp en exempel fil och konfigurerar den enligt anvisningarna.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under **anpassade loggar** i `SquidProxy_CL` tabellen.

Se **Nästa steg** -fliken på kopplings sidan för några användbara exempel frågor.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter squid proxy till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
