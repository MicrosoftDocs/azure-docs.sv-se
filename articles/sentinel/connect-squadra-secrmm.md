---
title: Anslut Squadra Technologies secRMM data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Squadra Technologies secRMM-data till Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: a00b4b1e81c0d644cf1475aa46dda3848fda1365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632909"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Anslut dina Squadra Technologies secRMM-data till Azure Sentinel 

Med Squadra Technologies secRMM Connector kan du enkelt ansluta Squadra Technologies secRMM säkerhets lösnings loggar med Azure Sentinel. Du kan visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Den här anslutnings tjänsten ger dig insikter om händelser för flyttbara USB-lagring. Integreringen mellan Squadra Technologies secRMM och Azure Sentinel använder REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Konfigurera och Anslut Squadra Technologies secRMM 

Squadra Technologies secRMM kan integrera och exportera loggar direkt till Azure Sentinel.
1. I Azure Sentinel-portalen klickar du på data kopplingar och väljer Squadra Technologies secRMM och öppnar sedan kopplings sidan.

2. Följ stegen som beskrivs i [onboarding-guiden för Squadra Technologies för Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) för att hämta Squadra secRMM-data i Azure Sentinel.   

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **CustomLogs** i `secRMM_CL` tabellen.

Om du vill fråga Squadra Technologies secRMM-loggar anger du tabell namnet överst i frågefönstret.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Squadra Technologies secRMM till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
