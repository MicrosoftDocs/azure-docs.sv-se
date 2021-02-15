---
title: Anslut Proofpoint riktad attack Protection (KNACKa) data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Proofpoint riktad attack Protection (KNACKa) data till Azure Sentinel.
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
ms.openlocfilehash: 86018bafaa42eac01e5dccf8da1d290b64e2475c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100092986"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Anslut din Proofpoint tryck till Azure Sentinel med Azure Function

> [!IMPORTANT]
> Proofpoint tryck på data koppling i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Via anslutningen Proofpoint Target attack Protection (Knack) kan du enkelt ansluta alla dina [Proofpoint](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) med hjälp av säkerhets lösningar med Azure Sentinel, Visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan Proofpoint KNACKNING och Azure Sentinel använder Azure Functions för att hämta loggdata med REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-proofpoint-tap"></a>Konfigurera och ansluta Proofpoint-tryck

Azure Functions kan integrera och hämta händelser och loggar direkt från Proofpoint att trycka och vidarebefordra dem till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **Proofpoint tryck på** koppling.

1. Välj **Öppna kopplings sida**.

1. Följ anvisningarna på sidan **PROOFPOINT knackning** .

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under tabellerna **ProofpointTAPMessagesBlocked_CL**, **ProofpointTAPMessagesDelivered_CL**, **ProofpointTAPClicksPermitted_CL** och **ProofpointTAPClicksBlocked_CL** .

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Proofpoint till Azure Sentinel med Azure Function Apps. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
