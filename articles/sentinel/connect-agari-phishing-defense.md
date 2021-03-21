---
title: Anslut din agar-lösning för phishing-skydd och märkes skydd till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder agar-skydd och varumärkes skydds anslutning för att hämta sina loggar till Azure Sentinel. Visa agar-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a60a0291d6669b2a9115dffa8e0d4d63fae4a440
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724446"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Anslut din agar-lösning för phishing-skydd och märkes skydd till Azure Sentinel

> [!IMPORTANT]
> Agar-skyddet för nätfiske och varumärkes skydd är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Med agar-skydds-och varumärkes skydd-anslutningen kan du enkelt ansluta dina loggar för varumärkes skydd och phishing-försvar till Azure Sentinel, så att du kan visa data i arbets böcker, fråga den för att skapa anpassade aviseringar och införliva den för att förbättra undersökningen. Agar-lösningar integreras med Azure Sentinel med hjälp av Azure Functions och REST API.

Dessutom kan kunder med varumärkes skydd och phishing-svar dra nytta av delning av hot information via säkerhets Graph API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att ansluta Agari lösningar för skydd mot nätfiske och varumärkes skydd till Azure Sentinel:

- Läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Läs behörighet till delade nycklar för arbets ytan. [Läs mer om nycklar för arbets ytor](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Läs-och Skriv behörighet för Azure Functions för att skapa en Funktionsapp. [Läs mer om Azure Functions](../azure-functions/index.yml).

- Se till att du har ditt Agari **-klient-ID** och **hemliga nycklar** (identiskt i alla agar-lösningar). Mer information finns på [webbplatsen för agar-utvecklare](https://developers.agari.com/agari-platform/docs/quick-start) .

## <a name="configure-and-connect-agari-solutions"></a>Konfigurera och ansluta agar-lösningar 

Agari-lösningar kan integrera och exportera loggar direkt till Azure Sentinel med hjälp av en Azure-Funktionsapp.

> [!NOTE]
> Den här anslutningen använder Azure Functions för att ansluta till agar-lösningar för att hämta sina loggar till Azure Sentinel. Detta kan resultera i ytterligare kostnader för data inmatning. Mer information finns på sidan om [Azure Functions prissättning](https://azure.microsoft.com/pricing/details/functions/) .

1. **Samla in dina agar API-autentiseringsuppgifter:** 

    Se till att du har ditt Agari **-klient-ID** och **hemliga nycklar**. Instruktioner finns på [webbplatsen för agar-utvecklare](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials).

1. **Valfritt Aktivera säkerhets Graph API:** 

    Med Agari-Funktionsapp kan du dela Hot information med Azure Sentinel via säkerhets Graph API. Om du vill använda den här funktionen måste du aktivera [kopplingen för Sentinel Threat Intelligence-plattformar](connect-threat-intelligence.md) och [Registrera ett program](/graph/auth-register-app-v2) i Azure Active Directory.

    Den här processen ger dig tre delar av information som du kan använda när du distribuerar Funktionsapp nedan: **graf-ID**, **graf-klient-ID** och **grafiskt klient hemlighet**.

1. **Distribuera anslutnings tjänsten och tillhör ande Azure-Funktionsapp:** 

    1. I Azure Sentinel-portalen väljer du **data anslutningar**. Välj **agar phishing-försvar och varumärkes skydd (för hands version)** och **Öppna sedan kopplings sidan**.

    1. Under **konfiguration** kopierar du Azure Sentinel **-arbetsyte-ID: t** och **primär nyckeln** och klistrar in dem i undan.

    1. Välj **distribuera till Azure**. (Du kan behöva rulla ned för att hitta knappen.)

    1. Skärmen **Anpassad distribution** visas.

        - Ange din agar **-klient-ID** och **klient hemlighet** (hemliga nycklar)

        - Ange ditt Azure Sentinel **-arbetsyte-ID** och din **arbets yta nyckel** (primär nyckel) som du kopierade och lagt undan.

        - Välj **Sant** eller **falskt** för de agar-lösningar som du har aktiva prenumerationer för.

        - Om du har skapat en Azure Application för att dela IoCs med Azure Sentinel med hjälp av säkerhets Graph API väljer du **Sant** för **att aktivera säkerhets diagram delning** och anger klient **-ID**: t för grafen, **graf-klientens ID** och **grafens klient hemlighet**.

    1. Välj **Granska + skapa**. När verifieringen är klar klickar du på **skapa**.

1. **Tilldela de behörigheter som krävs för din Funktionsapp:**

    Agar-anslutningen använder en miljö variabel för att lagra tidsstämplar för logg åtkomst. För att programmet ska kunna skriva till den här variabeln måste behörigheter tilldelas till den tilldelade system identiteten.

    1. I Azure Portal navigerar du till **Funktionsapp**.

    1. På bladet **Funktionsapp** väljer du Funktionsapp i listan och väljer sedan **identitet** under **inställningar** i navigerings menyn i Funktionsapp.

    1. Ange **statusen** till **på på** fliken **systemtilldelad** . 

    1. Välj **Spara**, så visas knappen för **Azure-roll tilldelningar** . Klicka på den.

    1. På sidan **Azure-roll tilldelningar** väljer du **Lägg till roll tilldelning**. Ange **omfång** till **prenumeration**, välj din prenumeration i list rutan **prenumeration** och ange **roll** till **konfigurations data ägare för appar**. 

    1. Välj **Spara**.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under *CustomLogs* i följande tabeller: 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Om du vill fråga agar-lösningens data anger du ett av ovanstående tabell namn i frågefönstret.

Se **Nästa steg** -fliken på kopplings sidan för några användbara exempel frågor.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter lösningar för agar-skydd i phishing och märkes skydd till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.