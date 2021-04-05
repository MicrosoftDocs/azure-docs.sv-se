---
title: Element i e-postinbjudanen B2B – Azure Active Directory | Microsoft Docs
description: E-postmall för inbjudan till Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb134a2fb784e02f5e00c9e88ab0df1794489e0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860600"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementen i e-postinbjudanen B2B – Azure Active Directory

E-postinbjudningar är en viktig komponent för att ta fram partner till användare i B2B-samarbete i Azure AD. Även om det [inte krävs att du skickar ett e-postmeddelande för att bjuda in någon med B2B-samarbete](add-user-without-invite.md), ger användaren all information som de behöver för att fatta ett beslut om att godkänna din inbjudan. Det ger också en länk som de kan alltid referera till i framtiden när de behöver gå tillbaka till dina resurser.

![Skärm bild som visar e-postinbjudan om B2B](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Förklarar e-postmeddelandet

Nu ska vi titta på några av de olika elementen i e-postmeddelandet så att du vet hur du bäst använder funktionerna.

### <a name="subject"></a>Ämne

Ämnet för e-postmeddelandet följer det här mönstret:

&lt;användar namnet &gt; uppmanade dig att få åtkomst till program i organisationen.

### <a name="from-address"></a>Avsändaradress

Vi använder ett LinkedIn-liknande mönster för från-adressen. Detta mönster bör göra det tydligt att även om e-postmeddelandet kommer från invites@microsoft.com en annan organisation. Formatet är: Microsoft-inbjudningar  <invites@microsoft.com> eller Microsoft-inbjudningar å &lt; tenantname vägnar &gt;  <invites@microsoft.com> . 

### <a name="reply-to"></a>Svara på

Svars-e-postmeddelandet är inställt på inbjudans e-postadress när det är tillgängligt, så att svar på e-postmeddelandet skickar ett e-postmeddelande tillbaka till inbjudan.

### <a name="phishing-warning"></a>Nät fiske varning

E-postmeddelandet börjar med en kort varning till användaren om nätfiske, som varnar dem att de bara ska acceptera inbjudningar som de förväntar sig. Det är en bra idé att se till att de partner som du bjuder in inte blir förvånad i din inbjudan genom att nämna dem i förväg.

![Bild av nät fiske varningen i e-postmeddelandet](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information-and-invitation-message"></a>Inbjudans information och Inbjudnings meddelande

E-postmeddelandet innehåller namnet och den primära domän som är kopplad till organisationen som skickar inbjudan. Den här informationen bör hjälpa inbjudan att fatta ett välgrundat beslut om att godkänna inbjudan. Om deltagaren innehåller ett meddelande som en del av sin inbjudan när de [bjuder in en gäst användare till katalogen, gruppen eller appen](add-users-administrator.md) eller när de [använder Inbjudnings-API: t](customize-invitation-api.md)markeras meddelandet i huvud avsnittet av e-postmeddelandet. Här ingår också inbjudans namn och profil avbildning om de har ställt in ett. Själva meddelandet är ett text utrymme, så av säkerhets skäl bearbetar det inte HTML-taggar.

![Bild av Inbjudnings meddelandet i e-postmeddelandet](media/invitation-email-elements/invitation-message-inviters-info.png)

### <a name="accept-button-and-redirect-url"></a>Acceptera knapp och omdirigerings-URL

Nästa avsnitt i e-postmeddelandet innehåller information om var inbjudan ska tas när de accepterar inbjudan, samt en knapp för att göra det.  I framtiden kan inbjudna alltid använda den här länken för att återgå till dina resurser direkt.

![Bild av knappen acceptera och omdirigerings-URL i e-postmeddelandet](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Fot nots avsnitt

Sidfoten innehåller mer information om den inbjudan som skickas. Det finns alltid ett alternativ för inbjudan att blockera framtida inbjudningar. Om organisationen har [angett en sekretess policy](../fundamentals/active-directory-properties-area.md)visas länken till instruktionen här.  Annars visar en anteckning att organisationen inte har angett en sekretess policy.

![Bild av avsnittet sidfot i e-postmeddelandet](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>Blockera en organisation (prenumeration)

I inbjudan från en organisation innehåller sidfoten ett alternativ för att **blockera framtida inbjudningar**. En gäst användare kan välja den här länken för att blockera alla framtida inbjudningar från organisationen. Den här åtgärden lägger också till organisationen i användarens lista över avbrutna prenumerationer på [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) .

### <a name="viewing-organizations-youve-blocked"></a>Visa organisationer som du har blockerat

En gäst användare kan följa de här stegen för att visa eller exportera de organisationer som de har blockerat:

1. Gå till [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage).
2. Ange din e-postadress och följ inloggnings stegen för e-postautentisering med eng ång slö sen ord.
3. Visa de organisationer som du har blockerat eller exportera namnen med hjälp av kopiera och klistra in.
   > [!NOTE]
   > Om du vill tillåta en organisation som du har blockerat att bjuda in dig igen kan du välja organisation och välja **Nästa**.

## <a name="how-the-language-is-determined"></a>Hur språket fastställs

Det språk som visas för gäst användaren i e-postinbjudan avgörs av följande inställningar. De här inställningarna visas i prioritetsordning. Om en inställning inte är konfigurerad, bestämmer nästa inställning i listan språket.

- Egenskapen **messageLanguage** för objektet [invitedUserMessageInfo](/graph/api/resources/invitedusermessageinfo) om API för att skapa inbjudan används
-   Egenskapen **preferredLanguage** som angetts i gästens [användar objekt](/graph/api/resources/user)
-   **Meddelande språket** som anges i egenskaperna för gäst användarens hem klient organisation (endast för Azure AD-klienter)
-   **Meddelande språket** som anges i egenskaperna för resurs klienten

Om ingen av dessa inställningar har kon figurer ATS, använder språket engelska (US) som standard.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Hur lägger Azure Active Directory administratörer till B2B-samarbets användare?](add-users-administrator.md)
- [Hur lägger informations anställda till B2B-samarbets användare?](add-users-information-worker.md)
- [Inlösen av B2B-samarbets inbjudningar](redemption-experience.md)
- [Lägg till B2B-samarbets användare utan inbjudan](add-user-without-invite.md)
