---
title: Anslut Zimperium Mobile Threat försvar till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Zimperium Mobile Threat försvar till Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 7cbf1c52af1d2902ae0726fc0dd98dbf12cecc44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097457"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Anslut ditt Zimperium Mobile Threat försvar till Azure Sentinel


> [!IMPORTANT]
> Zimperium Mobile Threat försvar data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Zimperium Mobile Threat Threat Connector ger dig möjlighet att ansluta Zimperium hot-loggen med Azure Sentinel för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig mer information om din organisations mobila hot landskap och förbättrar dina säkerhets åtgärder.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Konfigurera och Anslut Zimperium Mobile Threat försvar

Zimperium Mobile Threat försvar kan integrera och exportera loggar direkt till **Azure Sentinel**.

1. I Azure Sentinel-portalen klickar du på data kopplingar och väljer **Zimperium Mobile Threat försvar**.
2. Välj **Öppna kopplings sida**.
3. Följ anvisningarna på sidan **Zimperium Mobile Threat Threat** Connector som sammanfattas på följande sätt.
 1. I zConsole klickar du på **Hantera** i navigerings fältet.
 2. Klicka på fliken **Integreringar**.
 3. Klicka på knappen **hot rapportering** och sedan på knappen **Lägg till integreringar** .
 4. Skapa integrationen genom att välja **Microsoft Azure Sentinel** från de tillgängliga integreringarna och ange arbetsyte-ID och primär nyckel för att ansluta till Azure Sentinel.
 5. Möjlighet att välja en filter nivå för de hot data som push-överför till Azure Sentinel är också tillgängligt. 

4. För ytterligare information, se [Zimperium kund support Portal](https://support.zimperium.com).


## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under CustomLogs ZimperiumThreatLog_CL och ZimperiumMitigationLog_CL.

Om du vill använda det relevanta schemat i Log Analytics för Zimperium Mobile Threat försvar söker du efter ZimperiumThreatLog_CL och ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Zimperium Mobile Threat försvar till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).

- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

Mer information om Zimperium finns i följande avsnitt:

- [Zimperium](https://zimperium.com)

- [Zimperium Mobile Security-blogg](https://blog.zimperium.com)

- [Zimperium kund support Portal](https://support.zimperium.com)

