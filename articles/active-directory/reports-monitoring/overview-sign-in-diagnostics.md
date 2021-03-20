---
title: Vad är inloggnings diagnosen för Azure Active Directory?
description: Innehåller en allmän översikt över den diagnostiska inloggningen i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/15/2020
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdef3e1f1a60c9eb0c751855837e9cbe77e015e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572297"
---
# <a name="what-is-the-sign-in-diagnostic-in-azure-ad"></a>Vad är inloggnings diagnosen i Azure AD?

Azure Active Directory (Azure AD) ger dig en flexibel säkerhets modell som styr vad användarna kan göra med hanterade resurser. Åtkomst till dessa resurser styrs inte bara av *vem* de är, utan även av *hur* de kommer åt dem. Vanligt vis har en flexibel modell en viss komplexitets grad på grund av antalet konfigurations alternativ som du har. Komplexiteten ger möjlighet att öka risken för fel.

Som IT-administratör behöver du en lösning som ger dig inblick i aktiviteterna i systemet. Den här synligheten kan göra det möjligt att diagnostisera och lösa problem när de inträffar. Inloggnings diagnostiken för Azure AD är ett exempel på en sådan lösning. Du kan använda diagnostiken för att analysera vad som hände under ett inloggnings försök och få rekommendationer för att lösa problem utan att behöva involvera Microsoft support.

Den här artikeln ger en översikt över vad lösningen gör och hur du kan använda den.

## <a name="requirements"></a>Krav

Den diagnostiska inloggningen är tillgänglig i alla versioner av Azure AD.

Du måste vara global administratör i Azure AD för att kunna använda den.

## <a name="how-it-works"></a>Så här fungerar det

I Azure AD är svaret på ett inloggnings försök bundet till *som* loggar in och *hur* de får åtkomst till klienten. En administratör kan till exempel normalt konfigurera alla aspekter av klienten när de loggar in från företags nätverket. Men samma användare kan vara blockerad när de loggar in med samma konto från ett ej betrott nätverk.

På grund av den större flexibiliteten i systemet för att svara på ett inloggnings försök, kan det hända att du får fel söknings scenarier där du behöver felsöka inloggningar. Den inloggade diagnostiken är en funktion som:

- Analyserar data från inloggnings händelser.

- Visar vad som hände.

- Innehåller rekommendationer för hur du löser problem.

Den diagnostiska inloggningen för Azure AD är utformad för att möjliggöra självdiagnostik av inloggnings fel. För att slutföra diagnos processen måste du:

![Diagram som visar den diagnostiska inloggningen.](./media/overview-sign-in-diagnostics/process.png)

1. Definiera omfånget för de inloggnings händelser du bryr dig om.

2. Välj den inloggning som du vill granska.

3. Granska diagnostiska resultat.

4. Vidta åtgärder.

### <a name="define-scope"></a>Definiera omfång

Målet med det här steget är att definiera omfånget för inloggnings händelserna som ska undersökas. Ditt omfång är antingen baserat på en användare eller en identifierare (correlationId, requestId) och ett tidsintervall. Om du vill begränsa omfånget ytterligare kan du ange ett namn på appen. I Azure AD används omfattnings informationen för att hitta rätt händelser.  

### <a name="select-sign-in"></a>Välj inloggning  

Baserat på dina Sök villkor hämtar Azure AD alla matchande inloggnings händelser och visar dem i en lista över Sammanfattning för autentisering.

![En del skärm bild som visar sammanfattnings avsnittet för autentisering.](./media/overview-sign-in-diagnostics/authentication-summary.png)

Du kan anpassa de kolumner som visas i den här vyn.

### <a name="review-diagnostic"></a>Granska diagnostik

För den valda inloggnings händelsen ger Azure AD dig diagnostiska resultat.

![En del skärm bild som visar avsnittet diagnostiska resultat.](./media/overview-sign-in-diagnostics/diagnostics-results.png)

De här resultaten börjar med en utvärdering, som förklarar vad som hände i några få meningar. Förklaringen hjälper dig att förstå hur systemet fungerar.

Därefter får du en översikt över de relaterade principerna för villkorlig åtkomst som tillämpades på den valda inloggnings händelsen. Diagnostiska resultat innehåller även rekommenderade reparations steg för att lösa problemet. Eftersom det inte alltid är möjligt att lösa problem utan mer hjälp kan ett rekommenderat steg vara att öppna ett support ärende.

### <a name="take-action"></a>Vidta åtgärd

Nu bör du ha den information du behöver för att åtgärda problemet.

## <a name="scenarios"></a>Scenarier

Följande scenarier täcks av inloggnings diagnostiken:

- Blockeras av villkorlig åtkomst

- Villkorlig åtkomst misslyckades

- Multifaktorautentisering (MFA) från villkorlig åtkomst

- MFA från andra krav

- MFA-korrektur krävs

- MFA-korrektur som krävs (riskfylld inloggnings plats)

- Lyckad inloggning

### <a name="blocked-by-conditional-access"></a>Blockeras av villkorlig åtkomst

I det här scenariot har ett inloggnings försök blockerats av en princip för villkorlig åtkomst.

![Skärm bild som visar åtkomst konfiguration med blockera åtkomst valt.](./media/overview-sign-in-diagnostics/block-access.png)

Diagnostic-avsnittet i det här scenariot visar information om användar inloggnings händelsen och tillämpade principer.

### <a name="failed-conditional-access"></a>Villkorlig åtkomst misslyckades

Det här scenariot är vanligt vis ett resultat av ett inloggnings försök som misslyckats på grund av att kraven för en princip för villkorlig åtkomst inte uppfylldes. Vanliga exempel:

![Skärm bild som visar åtkomst konfiguration med vanliga princip exempel och bevilja åtkomst valt.](./media/overview-sign-in-diagnostics/require-controls.png)

- Kräv hybrid Azure AD-ansluten enhet

- Kräv godkänd klientapp

- Kräva appskyddsprincip

Diagnostic-avsnittet i det här scenariot visar information om användarens inloggnings försök och tillämpade principer.

### <a name="mfa-from-conditional-access"></a>MFA från villkorlig åtkomst

I det här scenariot krävs en princip för villkorlig åtkomst för att logga in med multifaktorautentisering.

![Skärm bild som visar åtkomst konfiguration med Kräv multifaktorautentisering markerat.](./media/overview-sign-in-diagnostics/require-mfa.png)

Diagnostic-avsnittet i det här scenariot visar information om användarens inloggnings försök och tillämpade principer.

### <a name="mfa-from-other-requirements"></a>MFA från andra krav

I det här scenariot tvingas ett krav för multifaktorautentisering inte tillämpas av en princip för villkorlig åtkomst. Till exempel multifaktorautentisering för varje användare.

![Skärm bild som visar multifaktorautentisering per användar konfiguration.](./media/overview-sign-in-diagnostics/mfa-per-user.png)

Syftet med det här diagnostiska scenariot är att ge mer information om:

- Källan för avbrottet för multifaktorautentisering
- Resultatet av klient interaktionen

Du kan också visa all information om användarens inloggnings försök.

### <a name="mfa-proof-up-required"></a>MFA-korrektur krävs

I det här scenariot avbröts inloggnings försöken av förfrågningar om att konfigurera multifaktorautentisering. Den här konfigurationen kallas även för ett korrektur.

Verifiering av multifaktorautentisering sker när en användare måste använda multifaktorautentisering men inte har konfigurerat den ännu, eller så måste en administratör ha krävt att användaren konfigurerar den.

Syftet med det här diagnostiska scenariot är att visa att multifaktorautentisering för multifaktorautentisering berodde på bristande användar konfiguration. Den rekommenderade lösningen är att användaren ska kunna slutföra korrekturet.

### <a name="mfa-proof-up-required-risky-sign-in-location"></a>MFA-korrektur som krävs (riskfylld inloggnings plats)

I det här scenariot avbröts inloggnings försöket av en begäran om att konfigurera multifaktorautentisering från en riskfylld inloggnings plats.

Syftet med det här diagnostiska scenariot är att visa att multifaktorautentisering för multifaktorautentisering berodde på bristande användar konfiguration. Den rekommenderade lösningen är att användaren ska kunna slutföra korrekturet, särskilt från en nätverks plats som inte ser riskfylld ut.

Om t. ex. ett företags nätverk har definierats som en namngiven plats, bör användaren försöka utföra korrekturet från företags nätverket i stället.

### <a name="successful-sign-in"></a>Lyckad inloggning

I det här scenariot avbröts inloggnings händelser inte av villkorlig åtkomst eller multifaktorautentisering.

Det här diagnostiska scenariot innehåller information om användar inloggnings händelser som förväntades avbrytas på grund av principer för villkorlig åtkomst eller multifaktorautentisering.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Active Directory-rapporter?](overview-reports.md)
- [Vad är Azure Active Directory-övervakning?](overview-monitoring.md)
