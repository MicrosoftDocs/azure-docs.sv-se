---
title: Slutanvändarupplevelser för program – Azure Active Directory
description: Azure Active Directory (Azure AD) erbjuder flera anpassningsbara sätt att distribuera program till slutanvändare i din organisation.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iangithinji
ms.reviewer: arvindh
ms.openlocfilehash: c555899a65a5e8cf4c8fcc6214e4dcbda3931f08
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374241"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Slutanvändarupplevelser för program i Azure Active Directory

Azure Active Directory (Azure AD) erbjuder flera anpassningsbara sätt att distribuera program till slutanvändare i din organisation:

* Azure AD-Mina appar
* Microsoft 365 programstartprogram
* Direkt inloggning till federerade appar
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Vilken eller vilka metoder du väljer att distribuera i din organisation är ditt eget gottfinnande.

## <a name="azure-ad-my-apps"></a>Azure AD-Mina appar

Mina appar på är en webbaserad portal som gör att en användare med ett organisationskonto i Azure Active Directory kan visa och starta program som de har beviljats åtkomst till av https://myapps.microsoft.com Azure AD-administratören. Om du är en slutanvändare med [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)kan du också använda grupphanteringsfunktioner via självbetjäning via Mina appar.

Som standard visas alla program tillsammans på en enda sida. Men du kan använda samlingar för att gruppera relaterade program och presentera dem på en separat flik, vilket gör dem lättare att hitta. Du kan till exempel använda samlingar för att skapa logiska grupper av program för specifika jobbroller, aktiviteter, projekt och så vidare. Mer information finns i [Skapa samlingar på Mina appar portalen.](access-panel-collections.md) 

Mina appar är separat från Azure Portal och kräver inte att användarna har en Azure-prenumeration eller Microsoft 365 prenumeration.

Mer information om Azure AD Mina appar finns i [introduktionen till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 programstartprogram

För organisationer som har distribuerat Microsoft 365 visas även program som tilldelats till användare via Azure AD i Office 365-portalen på [https://portal.office.com/myapps](https://portal.office.com/myapps) . Detta gör det enkelt och praktiskt för användare i en organisation att starta sina appar utan att behöva använda en andra portal, och är den rekommenderade appstartlösningen för organisationer som använder Microsoft 365.

Mer information om Office 365-programstartaren finns i Få appen att visas i [Office 365-appstartaren.](/previous-versions/office/office-365-api/)

## <a name="direct-sign-on-to-federated-apps"></a>Direkt inloggning till federerade appar

De flesta federerade program som stöder SAML 2.0, WS-Federation eller OpenID Connect stöder också möjligheten för användare att starta vid programmet och sedan logga in via Azure AD antingen genom automatisk omdirigering eller genom att klicka på en länk för att logga in. Detta kallas tjänstleverantörsinitierad inloggning och de flesta federerade program i Azure AD-programgalleriet stöder detta (mer information finns i dokumentationen som är länkad från appens konfigurationsguide för enkel inloggning i Azure Portal).

## <a name="direct-sign-on-links"></a>Länkar för direkt inloggning

Azure AD stöder också direkta länkar för enkel inloggning till enskilda program som stöder lösenordsbaserad enkel inloggning, länkad enkel inloggning och alla former av federerad enkel inloggning.

Dessa länkar är specifikt utformade URL:er som skickar en användare via Azure AD-inloggningsprocessen för ett visst program utan att användaren behöver starta dem från Azure AD Mina appar eller Microsoft 365. Dessa **URL:er för användaråtkomst** finns under egenskaperna för tillgängliga företagsprogram. I Azure Portal väljer du **Azure Active Directory**  >  **Företagsprogram**. Välj programmet och välj sedan **Egenskaper.**

![Exempel på URL för användaråtkomst i Twitter-egenskaper](media/end-user-experiences/direct-sign-on-link.png)

Dessa länkar kan kopieras och klistras in var som helst där du vill ange en inloggningslänk till det valda programmet. Detta kan vara i ett e-postmeddelande eller i en anpassad webbaserad portal som du har ställt in för åtkomst till användarprogram. Här är ett exempel på en url för enkel inloggning med Azure AD för Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

På samma sätt som med organisationsspecifika URL:er för Mina appar kan du anpassa den här URL:en ytterligare genom att lägga till en av de aktiva eller verifierade domänerna för din katalog efter myapps.microsoft.com domänen.  Detta säkerställer att all organisations varumärke läses in direkt på inloggningssidan utan att användaren behöver ange sitt användar-ID först:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

När en behörig användare klickar på någon av dessa programspecifika länkar ser de först sin organisations inloggningssida (förutsatt att de inte redan är inloggade) och efter inloggningen omdirigeras de till appen utan att de behöver stoppas Mina appar först. Om användaren saknar förutsättningar för att få åtkomst till programmet, till exempel det lösenordsbaserade webbläsartillägget för enkel inloggning, uppmanas användaren att installera det saknade tillägget. Länk-URL:en förblir också konstant om konfigurationen för enkel inloggning för programmet ändras.

Dessa länkar använder samma åtkomstkontrollmekanismer som Mina appar och Microsoft 365, och endast de användare eller grupper som har tilldelats till programmet i Azure Portal kan autentiseras. Användare som är obehöriga ser dock ett meddelande som förklarar att de inte har beviljats åtkomst och får en länk för att läsa in Mina appar för att visa tillgängliga program som de har åtkomst till.

## <a name="next-steps"></a>Nästa steg

* [Snabbstartsserie om programhantering](view-applications-portal.md)
* [Vad är enkel inloggning?](what-is-single-sign-on.md)
* [Kom igång Azure Active Directory integrering med program](plan-an-application-integration.md)