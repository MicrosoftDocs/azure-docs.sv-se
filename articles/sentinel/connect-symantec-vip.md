---
title: Anslut Symantec VIP-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Symantec VIP-data till Azure Sentinel.
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
ms.openlocfilehash: c7429108f70d735cb6e314a0d4daa27ba0d31637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100090419"
---
# <a name="connect-your-symantec-vip-to-azure-sentinel"></a>Anslut din Symantec-VIP till Azure Sentinel

> [!IMPORTANT]
> Symantec VIP data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln förklarar hur du ansluter din [SYMANTEC VIP](https://vip.symantec.com/) -installation till Azure Sentinel. Med Symantec VIP data Connector kan du enkelt ansluta dina Symantec VIP-loggar med Azure Sentinel, Visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan Symantec VIP och Azure Sentinel använder syslog.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="forward-symantec-vip-logs-to-the-syslog-agent"></a>Vidarebefordra Symantec VIP-loggar till syslog-agenten  

Konfigurera Symantec VIP för att vidarebefordra syslog-meddelanden till Azure-arbetsytan via syslog-agenten.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **Symantec VIP** -anslutning.

1. Välj **Öppna kopplings sida**.

1. Följ instruktionerna på sidan **SYMANTEC VIP** .

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under syslog.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Symantec VIP-loggar till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.