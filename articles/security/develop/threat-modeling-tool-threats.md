---
title: Hot – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Sidan hot kategori för Microsoft Threat Modeling Tool som innehåller kategorier för alla utsatta genererade hot.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727834"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft Threat Modeling Tool hot

Threat Modeling Tool är ett kärn element i Microsoft Security Development Lifecycle (SDL). Det gör att Software Architects kan identifiera och minska potentiella säkerhets problem tidigt, när de är relativt enkla och kostnads effektiva att lösa. Det innebär att den totala kostnaden för utveckling minskar avsevärt. Dessutom utformade vi verktyget med icke-säkerhetsexperter i åtanke, vilket gör det enklare för alla utvecklare att skapa och analysera hot modeller.

> Gå till **[Threat Modeling Tool](threat-modeling-tool.md)** och kom igång redan idag!

Threat Modeling Tool hjälper dig att besvara vissa frågor, till exempel de som visas nedan:

* Hur kan en angripare ändra autentiseringsdata för data?
* Vad är effekten om en angripare kan läsa användar profil data?
* Vad händer om åtkomst nekas till användar profil databasen?

## <a name="stride-model"></a>Kliv modell

För att bättre hjälpa dig att formulera dessa typer av Uddig frågor använder Microsoft kliv-modellen, som kategoriserar olika typer av hot och fören klar de övergripande säkerhets konversationerna.

| Kategori | Beskrivning |
| -------- | ----------- |
| **Förfalskning** | Inbegriper otillåten åtkomst och använder sedan en annan användares autentiseringsinformation, till exempel användar namn och lösen ord |
| **Manipulation** | Inbegriper skadlig ändring av data. Exempel på detta är otillåtna ändringar av beständiga data, till exempel den som finns i en databas och ändring av data som flödar mellan två datorer i ett öppet nätverk, till exempel Internet |
| **Avvislighet** | Kopplad till användare som nekar en åtgärd utan att andra parter har något sätt att bevisa, till exempel att en användare utför en olaglig åtgärd i ett system som saknar möjlighet att spåra förbjudna åtgärder. Oavvislig het syftar på möjligheten för ett system att räkna med oavvislig hets hot. En användare som köper ett objekt kan till exempel behöva signera för objektet vid inbetalning. Leverantören kan sedan använda det signerade kvittot som bevis på att användaren fick paketet |
| **Avslöjande av information** | Inbegriper exponering av information till personer som inte ska ha åtkomst till den, till exempel möjligheten för användare att läsa en fil som de inte har beviljats åtkomst till, eller att en inkräktare kan läsa data i överföring mellan två datorer |
| **Denial of Service** | DOS-attacker (Denial of Service) är nekade för giltiga användare, till exempel genom att göra en webb server tillfälligt otillgänglig eller oanvändbar. Du måste skydda mot vissa typer av DoS-hot om du bara vill förbättra systemets tillgänglighet och tillförlitlighet |
| **Behörighetshöjning** | En obehörig användare får privilegie rad åtkomst och har därmed tillräcklig åtkomst för att kompromettera eller förstöra hela systemet. Höjning av privilegierade hot omfattar de situationer där en angripare faktiskt har trängt in på alla system försvar och blir en del av det betrodda systemet, en farlig situation |

## <a name="next-steps"></a>Nästa steg

Fortsätt till **[Threat Modeling Tool](threat-modeling-tool-mitigations.md)** åtgärder för att lära dig om de olika sätt som du kan vidta för att undvika dessa hot med Azure.