---
title: Anslut CyberArk EPV-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder CyberArk Enterprise Password Vault (EPV) data Connector för att hämta loggar till Azure Sentinel. Visa CyberArk EPV-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: aa57963ce369e4c8f84f4aae5f99fe343181ff6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530509"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>Ansluta CyberArk Enterprise Password Vault (EPV) till Azure Sentinel

> [!IMPORTANT]
> CyberArk Enterprise Password Vault (EPV) data Connector i Azure Sentinel är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med CyberArk syslog-anslutningsprogrammet kan du enkelt ansluta alla dina CyberArk-säkerhetslösnings loggar med din Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan CyberArk och Azure Sentinel använder CEF data Connector för att korrekt parsa och Visa CyberArk syslog-meddelanden.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-cyberark-epv"></a>Konfigurera och ansluta CyberArk-EPV

CyberArk EPV-loggar skickas från valvet till en Linux-baserad logg vidarebefordrande server (som kör rsyslog eller syslog-ng) med Log Analytics-agenten installerad, som exporterar loggarna till Azure Sentinel. Om du inte har en sådan logg vidarebefordrande server kan du läsa [de här anvisningarna](connect-cef-agent.md) för att skapa en igång.

1. I Azure Sentinel-portalen klickar du på **data kopplingar**, väljer **CyberArk Enterprise Password Vault (EPV) Events (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ [CYBERARK EPV-instruktionerna](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) för att konfigurera att skicka syslog-data till servern för logg vidarebefordran.

1. Verifiera anslutningen och kontrol lera data inmatningen med hjälp av [de här anvisningarna](connect-cef-verify.md). Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **Azure Sentinel** i tabellen *CommonSecurityLog* .

Om du vill fråga CyberArk EPV-loggarna i Log Analytics anger du `CommonSecurityLog` överst i frågefönstret.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter CyberArk Enterprise Password Vault-loggar till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
