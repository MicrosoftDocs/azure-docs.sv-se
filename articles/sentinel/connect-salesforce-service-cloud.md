---
title: Anslut Salesforce-tjänstens moln data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Salesforce-tjänsten Cloud Data Connector för att hämta Salesforce-loggar till Azure Sentinel. Visa Salesforce-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.openlocfilehash: 1efd91d92bac1bc1f39d82aaa0cc71daa0275f8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570543"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>Anslut ditt Salesforce-tjänst moln till Azure Sentinel

> [!IMPORTANT]
> Salesforce-tjänstens moln koppling är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter din moln lösning för Salesforce-tjänster till Azure Sentinel. Med data kopplingen för Salesforce-tjänsten kan du enkelt ansluta dina Salesforce-data med Azure Sentinel, så att du kan visa dem i arbets böcker, använda dem för att skapa anpassade aviseringar och införliva dem för att förbättra undersökningen. Integreringen mellan Salesforce-tjänstens moln och Azure Sentinel använder REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha Läs behörighet till delade nycklar för arbets ytan. [Läs mer om nycklar för arbets ytor](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Du måste ha läs-och Skriv behörighet till Azure Functions för att kunna skapa en Funktionsapp. [Läs mer om Azure Functions](../azure-functions/index.yml).

- Du måste ha följande Salesforce-REST API autentiseringsuppgifter: **Salesforce API-användarnamn**, **Salesforce API-lösenord**, Salesforce- **säkerhetstoken**, **Salesforce-konsument nyckel**, **Salesforce-konsument hemlighet**. [Läs mer om Salesforce REST API](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm).

## <a name="configure-and-connect-salesforce-service-cloud"></a>Konfigurera och Anslut Salesforce-tjänstens moln

Salesforce-tjänstemolnet kan integrera och exportera loggar direkt till Azure Sentinel.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. I galleriet **data anslutningar** väljer du **Salesforce-tjänstens moln (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ stegen som beskrivs i **konfigurations** avsnittet på kopplings sidan.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **CustomLogs** i `SalesforceServiceCloud_CL` tabellen.

Se **Nästa steg** -fliken på kopplings sidan för några användbara exempel frågor.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Salesforce-tjänstemolnet till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.