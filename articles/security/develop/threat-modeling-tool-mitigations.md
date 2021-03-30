---
title: Åtgärder – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Sidan lösningar för Microsoft Threat Modeling Tool framhäva möjliga lösningar på de mest utsatta genererade hoten.
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
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "68728032"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft Threat Modeling Tool begränsningar

Threat Modeling Tool är ett kärn element i Microsoft Security Development Lifecycle (SDL). Det gör att Software Architects kan identifiera och minska potentiella säkerhets problem tidigt, när de är relativt enkla och kostnads effektiva att lösa. Det innebär att den totala kostnaden för utveckling minskar avsevärt. Dessutom utformade vi verktyget med icke-säkerhetsexperter i åtanke, vilket gör det enklare för alla utvecklare att skapa och analysera hot modeller.

Gå till **[Threat Modeling Tool](threat-modeling-tool.md)** och kom igång redan idag!

## <a name="mitigation-categories"></a>Minsknings kategorier

Threat Modeling Tools begränsningar kategoriseras enligt säkerhets ramen för webb program, som består av följande:

| Kategori | Beskrivning |
| -------- | ----------- |
| **[Granskning och loggning](threat-modeling-tool-auditing-and-logging.md)** | Vem gjorde vad och när? Granskning och loggning avser hur ditt program registrerar säkerhetsrelaterade händelser |
| **[Autentisering](threat-modeling-tool-authentication.md)** | Vem är du? Autentisering är den process där en entitet bevisar identiteten för en annan entitet, vanligt vis via autentiseringsuppgifter, till exempel ett användar namn och lösen ord |
| **[Auktorisering](threat-modeling-tool-authorization.md)** | Vad kan du göra? Auktorisering är hur ditt program ger åtkomst kontroller för resurser och åtgärder |
| **[Kommunikations säkerhet](threat-modeling-tool-communication-security.md)** | Vem pratar om? Kommunikations säkerhet säkerställer att all kommunikation är så säker som möjligt |
| **[Konfigurations hantering](threat-modeling-tool-configuration-management.md)** | Vem kör ditt program som? Vilka databaser ansluter till? Hur administreras ditt program? Hur skyddas de här inställningarna? Konfigurations hantering avser hur ditt program hanterar dessa drift problem |
| **[Kryptografi](threat-modeling-tool-cryptography.md)** | Hur håller du hemligheter (konfidentialitet)? Hur manipuleras dina data eller bibliotek (integritet)? Hur tillhandahåller du frön för slumpmässiga värden som måste vara kryptografiskt starka? Kryptografi avser hur ditt program upprätthåller konfidentialitet och integritet |
| **[Undantags hantering](threat-modeling-tool-exception-management.md)** | Vad gör ditt program när ett metod anrop i ditt program Miss lyckas? Hur mycket avslöjar du? Returnerar du fel information till slutanvändarna? Skickar du värdefull undantags information tillbaka till anroparen? Fungerar ditt program inte korrekt? |
| **[Verifiering av verifiering](threat-modeling-tool-input-validation.md)** | Hur vet du att inmatade program får giltig och säker? Verifiering av indata avser hur ditt program filtrerar, sveper eller avvisar indata innan ytterligare bearbetning. Överväg att begränsa indata via Start punkter och att koda utdata genom avsluts punkter. Litar du på data från källor som databaser och fil resurser? |
| **[Känsliga data](threat-modeling-tool-sensitive-data.md)** | Hur hanterar programmet känsliga data? Känsliga data syftar på hur ditt program hanterar data som måste skyddas antingen i minnet, i nätverket eller i beständiga butiker |
| **[Sessionshantering](threat-modeling-tool-session-management.md)** | Hur hanterar programmet och skyddar användarsessioner? En session refererar till en serie relaterade interaktioner mellan en användare och ditt webb program |

Detta hjälper dig att identifiera:

* Var är de vanligaste misstagen
* Var finns de mest användbara förbättringarna

Därför kan du använda dessa kategorier för att fokusera och prioritera ditt säkerhets arbete, så om du vet att de vanligaste säkerhets problemen uppstår i verifierings-, autentiserings-och auktoriseringsregler, kan du starta där. Mer information finns på **[den här patent länken](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Nästa steg

Besök **[Threat Modeling Tool hot](threat-modeling-tool-threats.md)** för att lära dig mer om de hot kategorier som verktyget använder för att generera möjliga design hot.