---
title: Ansluta puls ansluta säkra data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter säkra data från Pulse Connect till Azure Sentinel.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 71e1c7787243713b29be9455fee966eff54f6d90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097661"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Anslut din puls anslutning säkert till Azure Sentinel

> [!IMPORTANT]
> Pulse Connect Secure data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln förklarar hur du ansluter en säker dator med [Pulse Connect](https://www.pulsesecure.net/products/pulse-connect-secure/) till Azure Sentinel. Med kopplings säkra data Connector i Pulse kan du enkelt ansluta dina puls säkra loggar med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan Pulse Connect Secure och Azure Sentinel använder syslog.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Forward Pulse Connect säkra loggar till syslog-agenten  

Konfigurera Pulse Connect Secure för att vidarebefordra syslog-meddelanden till Azure-arbetsytan via syslog-agenten.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **Pulse Connect säker** anslutning.

1. Välj **Öppna kopplings sida**.

1. Följ instruktionerna på sidan Aktivera **säker puls anslutning** .

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under syslog.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig att ansluta Pulse Connect säker till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.