---
title: Anslut svartvita moln sluts data för VMware kol till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter svarta slut punkts standard data för VMware-kol till Azure Sentinel.
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
ms.openlocfilehash: 2c26931db617109c36c19b73bd3ec9263f4b40cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096454"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Anslut din VMware kol Black Endpoint-standard till Azure Sentinel med Azure Function

> [!IMPORTANT]
> Den svarta kol-slutpunktens svarta slut punkts standard data koppling i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med den svarta slut punkts standard anslutningen för VMware-kol kan du enkelt ansluta alla dina säkerhets lösnings loggar för [VMware kol-svart slut punkt](https://www.carbonblack.com/products/endpoint-standard/) med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan VMware kol Black och Azure Sentinel använder Azure Functions för att hämta logg data med hjälp av REST API.


> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>Konfigurera och Anslut anslutnings punkts standarden för VMware kol Black

Azure Functions kan integrera och hämta händelser och loggar direkt från VMware kol Black Endpoint standard och vidarebefordra dem till Azure Sentinel.

1. Klicka på **data kopplingar** på Azure Sentinel-portalen och välj **VMware kol Black** Connector.
2. Välj **Öppna kopplings sida**.
3. Följ anvisningarna på svart sidan för **VMware-kol** .


## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under **CarbonBlackAuditLogs_CL**, **CarbonBlackNotifications_CL** och * * * * CarbonBlackEvents_CL * * * * tabeller.

## <a name="validate-connectivity"></a>Verifiera anslutning
Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter svart slut punkts standard för VMware kol till Azure Sentinel med Azure Function-appar. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

