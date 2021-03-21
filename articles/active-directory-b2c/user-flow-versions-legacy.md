---
title: Äldre användar flödes versioner i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om äldre versioner av användar flöden som är tillgängliga i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1fbe93c93b5ede2c6b031dab53a1450da473f802
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517811"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Äldre användar flödes versioner i Azure Active Directory B2C

> [!IMPORTANT]
> Den här artikeln beskriver den äldre versions metoden för användar flöden, som erbjuder v1-versioner (produktions klara) och V 1.1 och v2 (för hands version) av användar flöden. Andra miljöer än det offentliga Azure-molnet fortsätter att använda den här äldre versions metoden. I det offentliga Azure-molnet ersätts den här metoden av [nya **rekommenderade** och för **hands** versioner](user-flow-versions.md).
> 
Användar flöden i Azure Active Directory B2C (Azure AD B2C) hjälper dig att skapa gemensamma [principer](user-flow-overview.md) som fullständigt beskriver kund identitets upplevelser. Dessa upplevelser omfattar registrering, inloggning, lösen ords återställning eller profil redigering.

I tabellen nedan, om inte ett användar flöde identifieras som **rekommenderat**, betraktas det som en för *hands version*. Du bör endast använda rekommenderade användar flöden för dina produktions program.

## <a name="v1"></a>V1

| Användarflöde | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Lösenordsåterställning | Ja | Gör det möjligt för en användare att välja ett nytt lösen ord när de har verifierat sin e-post. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>Kompatibilitetsinställningar för token</li><li>[Krav för lösen ords komplexitet](password-complexity.md)</li></ul> |
| Redigera profil | Ja | Gör det möjligt för en användare att konfigurera sina användarattribut. Med det här användar flödet kan du konfigurera: <ul><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li></ul> |
| Logga in med ROPC | Inga | Gör det möjligt för en användare med ett lokalt konto att logga in direkt i interna program (ingen webbläsare krävs). Med det här användar flödet kan du konfigurera: <ul><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li></ul> |
| Logga in | Inga | Gör det möjligt för en användare att logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li><li>Blockera inloggning</li><li>Framtvinga lösen ords återställning</li><li>Håll mig inloggad (KMSI avgör)</ul><br>Du kan inte anpassa användar gränssnittet med det här användar flödet. |
| Registrera dig | Inga | Gör det möjligt för en användare att skapa ett konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li><li>[Krav för lösen ords komplexitet](password-complexity.md)</li></ul> |
| Registrera dig och logga in | Ja | Gör det möjligt för en användare att skapa ett konto eller logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li><li>[Krav för lösen ords komplexitet](password-complexity.md)</li></ul>|

## <a name="v11"></a>V 1.1

| Användarflöde | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Lösen ords återställning v 1.1 | Inga | Låter en användare välja ett nytt lösen ord när de har verifierat e-postmeddelandet (ny sidlayout är tillgänglig). Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>Kompatibilitetsinställningar för token</li><li>[Krav för lösen ords komplexitet](password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Användarflöde | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Lösen ords återställning v2 | Inga | Gör det möjligt för en användare att välja ett nytt lösen ord när de har verifierat sin e-post. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>Kompatibilitetsinställningar för token</li><li>[Åldershantering](age-gating.md)</li><li>[krav för lösen ords komplexitet](password-complexity.md)</li></ul> |
| Profil redigering v2 | Ja | Gör det möjligt för en användare att konfigurera sina användarattribut. Med det här användar flödet kan du konfigurera: <ul><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li></ul> |
| Logga in v2 | Inga | Gör det möjligt för en användare att logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li><li>[Åldershantering](age-gating.md)</li><li>Anpassning av inloggnings Sidan</li></ul> |
| Registrera dig v2 | Inga | Gör det möjligt för en användare att skapa ett konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li><li>[Åldershantering](age-gating.md)</li><li>[Krav för lösen ords komplexitet](password-complexity.md)</li></ul> |
| Registrera dig och logga in v2 | Inga | Gör det möjligt för en användare att skapa ett konto eller logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Åldershantering](age-gating.md)</li><li>[Krav för lösen ords komplexitet](password-complexity.md)</li></ul> |