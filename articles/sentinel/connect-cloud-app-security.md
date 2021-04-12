---
title: Anslut Cloud App Security data till Azure Sentinel | Microsoft Docs
description: Lär dig att använda Microsoft Cloud App Security-anslutningen (MCAS) för att strömma aviseringar och Cloud Discovery loggar från MCAS till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 3312eed71865508e5e83d37c7ced8cf220f13ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97835116"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Anslut data från Microsoft Cloud App Security 

Med [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) -anslutaren (MCAS) kan du strömma aviseringar och [Cloud Discovery loggar](/cloud-app-security/tutorial-shadow-it) från MCAS till Azure Sentinel. På så sätt kan du få insyn i dina molnappar, få avancerade analyser för att identifiera och bekämpa cyberhot och styra hur dina data överförs.

## <a name="prerequisites"></a>Förutsättningar

- Användaren måste ha läs-och Skriv behörighet på arbets ytan.
- Användaren måste ha behörighet som global administratör eller säkerhets administratör på arbets ytans klient organisation.
- Om du vill strömma Cloud Discovery loggar till Azure Sentinel [aktiverar du Azure Sentinel som Siem i Microsoft Cloud App Security](/cloud-app-security/siem-sentinel).

> [!IMPORTANT]
> Inmatning av Cloud Discovery loggar är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Anslut till Cloud App Security

Om du redan har Cloud App Security kontrollerar du att den är [aktive rad i nätverket](/cloud-app-security/getting-started-with-cloud-app-security).
Om Cloud App Security distribueras och matas in med dina data kan aviserings informationen enkelt strömmas i Azure Sentinel.


1. Välj **data kopplingar** på navigerings menyn i Azure Sentinel. I listan över kopplingar klickar du på panelen **Microsoft Cloud App Security** och sedan på knappen **Öppna kopplings sida** längst ned till höger.

1. Välj vilka loggar du vill strömma till i Azure Sentinel; Du kan välja **aviseringar** och **Cloud Discovery loggar** (för hands version). 

1. Klicka på **tillämpa ändringar**.

1. Du kan välja om du vill att aviseringarna från Cloud App Security automatiskt ska generera incidenter i Azure Sentinel. Under **skapa incidenter – rekommenderas!** väljer du **aktive rad** för att aktivera standard analys regeln som automatiskt skapar incidenter från aviseringar. Du kan sedan redigera regeln under **analys** på fliken  **aktiva regler** .

1. Om du vill använda det relevanta schemat i Log Analytics för Cloud App Security aviseringar skriver `SecurityAlert` du i frågefönstret. För schemat för Cloud Discovery loggar skriver du `McasShadowItReporting` .

> [!NOTE]
> Cloud Discovery hjälper till att identifiera och identifiera trender genom att aggregera data underliggande användarnas anslutningar till molnappar.
>
> Eftersom Cloud Discovery data aggregeras per dag bör du vara medveten om att upp till 24 timmar för de senaste data inte visas i Azure Sentinel. I händelse av att en undersökning på låg nivå kräver mer omedelbara data, bör det göras direkt i käll apparaten eller tjänsten där rå data finns.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Cloud App Security till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång med att identifiera hot med Azure Sentinel, med [inbyggda](./tutorial-detect-threats-built-in.md) eller [anpassade](tutorial-detect-threats-custom.md) regler.