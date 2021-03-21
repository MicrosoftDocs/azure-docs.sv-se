---
title: Anslut Juniper Networks SRX data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Juniper SRX data Connector för att hämta Juniper SRX-loggar till Azure Sentinel. Visa Juniper SRX-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530645"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Anslut din Juniper SRX-brandvägg till Azure Sentinel

> [!IMPORTANT]
> Juniper SRX-anslutningsprogrammet är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter din Juniper SRX-brandvägg till Azure Sentinel. Med Juniper SRX data Connector kan du enkelt ansluta SRX-loggar med Azure Sentinel, så att du kan visa data i arbets böcker, använda dem för att skapa anpassade aviseringar och lägga till dem för att förbättra undersökningen. Integreringen mellan Juniper SRX och Azure Sentinel använder syslog.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Din Juniper SRX-lösning måste konfigureras för att exportera loggar via syslog.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Vidarebefordra Juniper SRX-loggar till syslog-agenten  

Konfigurera Juniper-SRX för att vidarebefordra syslog-meddelanden till din Azure Sentinel-arbetsyta via syslog-agenten.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. I **data kopplings** galleriet väljer du ANSLUTNINGS **Juniper SRX (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ anvisningarna på anslutnings sidan för **JUNIPER SRX** :

    1. Installera och publicera agenten för Linux

        - Välj en virtuell Azure Linux-dator eller en icke-Azure Linux-dator (fysisk eller virtuell).

    1. Konfigurera loggarna som ska samlas in

        - Välj funktionerna och allvarlighets graderna i konfigurationen för arbets ytans agenter.

    1. Konfigurera och Anslut Juniper-SRX

        - Följ dessa anvisningar för att konfigurera Juniper-SRX för att vidarebefordra syslog.
            - [Trafik loggar (säkerhets princip loggar)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [System loggar](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - Använd IP-adressen för den Linux-dator som du installerade Linux-agenten på på fjärrservern.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under syslog.

Se **Nästa steg** -fliken på kopplings sidan för några användbara exempel frågor.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Juniper-SRX till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
