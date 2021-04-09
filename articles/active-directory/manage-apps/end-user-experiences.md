---
title: Slut användar upplevelse för program – Azure Active Directory
description: Azure Active Directory (Azure AD) tillhandahåller flera anpassningsbara sätt att distribuera program till slutanvändare i din organisation.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: 0d3f1cab0637c7409cbc45f4f5a5374b2b5b6a77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259261"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Slut användar upplevelse för program i Azure Active Directory

Azure Active Directory (Azure AD) tillhandahåller flera anpassningsbara sätt att distribuera program till slutanvändare i din organisation:

* Mina appar i Azure AD
* Microsoft 365 program start
* Direkt inloggning till federerade appar
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Vilken metod (er) du väljer att distribuera i din organisation är ditt eget gottfinnande.

## <a name="azure-ad-my-apps"></a>Mina appar i Azure AD

Mina appar på https://myapps.microsoft.com är en webbaserad portal som gör att en användare med ett organisations konto i Azure Active Directory kan visa och starta program som de har beviljats åtkomst till av Azure AD-administratören. Om du är en slutanvändare med [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)kan du även använda funktioner för grupp hantering via självbetjäning via Mina appar.

Som standard visas alla program på en enda sida. Men du kan använda samlingar för att gruppera relaterade program och presentera dem på en separat flik, vilket gör dem lättare att hitta. Du kan till exempel använda samlingar för att skapa logiska grupperingar av program för vissa jobb roller, aktiviteter, projekt och så vidare. Mer information finns i [skapa samlingar på portalen Mina appar](access-panel-collections.md). 

Mina appar är åtskilda från Azure Portal och kräver inte att användare har en Azure-prenumeration eller Microsoft 365 prenumeration.

Mer information om Mina appar i Azure AD finns i [introduktionen till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 program start

För organisationer som har distribuerat Microsoft 365 kommer program som tilldelas till användare via Azure AD också att visas i Office 365-portalen på [https://portal.office.com/myapps](https://portal.office.com/myapps) . Detta gör det enkelt och bekvämt för användare i en organisation att starta sina appar utan att behöva använda en andra Portal, och är den rekommenderade app-lansering för organisationer som använder Microsoft 365.

Mer information om Office 365 Application starta finns i se till att [din app visas i Office 365-appens start](/previous-versions/office/office-365-api/).

## <a name="direct-sign-on-to-federated-apps"></a>Direkt inloggning till federerade appar

De flesta federerade program som har stöd för SAML 2,0, WS-Federation eller OpenID Connect stöder även möjligheten för användare att starta i programmet och sedan sedan bli inloggad via Azure AD, antingen genom automatisk omdirigering eller genom att klicka på en länk för att logga in. Detta kallas för inaktiverad inloggning av tjänst leverantör och de flesta federerade program i Azure AD-programgalleriet stöder detta (se dokumentationen som är länkad från appens konfigurations guide för enkel inloggning i Azure Portal för mer information).

## <a name="direct-sign-on-links"></a>Direkta inloggnings länkar

Azure AD stöder även länkar med direkt enkel inloggning till enskilda program som stöder lösenordsbaserad enkel inloggning, länkad enkel inloggning och alla former av federerad enkel inloggning.

Dessa länkar är särskilt utformade URL: er som skickar en användare via inloggnings processen i Azure AD för ett specifikt program utan att användaren behöver starta dem från Azure AD Mina appar eller Microsoft 365. Dessa **URL: er för användar åtkomst** kan hittas under egenskaperna för tillgängliga företags program. I Azure Portal väljer du **Azure Active Directory**  >  **företags program**. Välj programmet och välj sedan **Egenskaper**.

![Exempel på URL för användar åtkomst i Twitter-egenskaper](media/end-user-experiences/direct-sign-on-link.png)

Dessa länkar kan kopieras och klistras in överallt där du vill ange en inloggnings länk till det valda programmet. Detta kan finnas i ett e-postmeddelande eller på en anpassad webbaserad portal som du har ställt in för åtkomst till användar program. Här är ett exempel på en Azure AD Direct-inloggnings-URL för Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Precis som för organisatoriska URL: er för Mina appar kan du anpassa denna URL ytterligare genom att lägga till en av de aktiva eller verifierade domänerna för katalogen efter *myapps.Microsoft.com* -domänen. Detta säkerställer att all organisations anpassning läses in direkt på inloggnings sidan utan att användaren behöver ange sitt användar-ID först:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

När en behörig användare klickar på någon av dessa programspecifika länkar ser de först sin organisations inloggnings sida (förutsatt att de inte redan är inloggade) och när inloggningen omdirigeras till appen utan att först stoppas i Mina appar. Om användaren saknar nödvändiga förutsättningar för att komma åt programmet, t. ex. lösen ord för den lösen ordsbaserade tillägget för enkel inloggning, uppmanas användaren att installera det saknade tillägget. Länk-URL: en förblir också konstant om konfigurationen för enkel inloggning för programmet ändras.

De här länkarna använder samma mekanismer för åtkomst kontroll som mina appar och Microsoft 365 och endast de användare eller grupper som har tilldelats till programmet i Azure Portal kan autentiseras. Användare som inte har behörighet kommer dock att se ett meddelande som förklarar att de inte har beviljats åtkomst och att de får en länk till att läsa in Mina appar för att visa tillgängliga program som de har åtkomst till.

## <a name="next-steps"></a>Nästa steg

* [Snabb starts serie för program hantering](view-applications-portal.md)
* [Vad är enkel inloggning?](what-is-single-sign-on.md)
* [Att integrera Azure Active Directory med program komma igång-guide](plan-an-application-integration.md)