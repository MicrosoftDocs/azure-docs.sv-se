---
title: Anslut Google workspace-data (G Suite) till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder data anslutningen Google Workspace (G Suite) för att mata in Google Workspace-aktiviteter i Azure Sentinel. Visa Google workspace-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746286"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Anslut din Google-arbetsyta till Azure Sentinel

> [!IMPORTANT]
> Google Workspace Connector är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Data kopplingen [Google Workspace (tidigare G Suite)](https://workspace.google.com/) ger möjlighet att mata in Google-arbetsytans aktivitets händelser i Azure Sentinel via REST API. Anslutningen ger dessa [händelser](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) synligheten i din SOC, som hjälper dig att undersöka potentiella säkerhets risker, analysera teamets användning av samarbete, diagnostisera konfigurations problem, spåra vem som loggar in och när du analyserar administratörs aktiviteten, förstår hur användare skapar och delar innehåll och granskar fler händelser i din organisation.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

Om du vill samla in Google workspace-data måste du ha följande behörigheter och autentiseringsuppgifter:

- Läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Läs behörighet till delade nycklar för arbets ytan. [Läs mer om nycklar för arbets ytor](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Läs-och Skriv behörighet till Azure Functions för att skapa en Funktionsapp. [Läs mer om Azure Functions](../azure-functions/index.yml).

- **GooglePickleString** -autentiseringsuppgiften krävs för REST API. [Läs mer om Google REST API](https://developers.google.com/admin-sdk/reports/v1/reference/activities). [Lär dig hur du hämtar autentiseringsuppgifter](https://developers.google.com/admin-sdk/reports/v1/quickstart/python).

## <a name="configure-and-connect-google-workspace"></a>Konfigurera och ansluta Google-arbetsytan

Google-arbetsytan kan integrera och exportera loggar direkt till Azure-kontroll med hjälp av en Azure-Funktionsapp.

> [!NOTE]
> Den här anslutningen använder Azure Functions för att ansluta till Google Reports-API: t för att hämta aktivitets händelser till Azure Sentinel. Detta kan resultera i ytterligare kostnader för data inmatning. Mer information finns på sidan om [Azure Functions prissättning](https://azure.microsoft.com/pricing/details/functions/) .

1. I Azure Sentinel-portalen klickar du på **data kopplingar**. 

1. Välj **Google Workspace (G Suite) (för hands version)** i kopplings galleriet och välj **Öppna kopplings sida**.

1. Följ stegen som beskrivs i **konfigurations** avsnittet på kopplings sidan.

## <a name="validate-connectivity-and-find-your-data"></a>Verifiera anslutningen och hitta dina data

Det kan ta upp till 20 minuter innan du kan se inmatade data i Azure Sentinel.

När en lyckad anslutning har upprättats visas informationen i **loggarna** under avsnittet **anpassade loggar** i följande tabeller:
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

Den här data kopplingen är beroende av en parser som baseras på en Kusto-funktion som fungerar som förväntat. [Följ de här stegen](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) för att skapa **GWorkspaceActivityReports** Kusto Functions-alias.

Se **Nästa steg** -fliken på kopplings sidan för några användbara exempel frågor.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Google-arbetsytan till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
