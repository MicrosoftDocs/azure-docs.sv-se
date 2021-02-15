---
title: Bästa praxis och rekommendationer för Azure Active Directory B2B
description: Lär dig metod tips och rekommendationer för Business-to-Business (B2B)-gäst användar åtkomst i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94fd488ceb7ddb3724dd576c97c9070481e95147
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365641"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Metod tips för Azure Active Directory B2B
Den här artikeln innehåller rekommendationer och metod tips för samarbete mellan företag (B2B) i Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > Med **början från oktober 2021** kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade ("virus" eller "just-in-Time") Azure AD-konton och-klienter för B2B-samarbets scenarier. Vid detta tillfälle aktive ras e-postfunktionen för eng ång slö sen ord för alla befintliga klienter och aktive ras som standard för nya klienter. Vi aktiverar e-postfunktionen för eng ång slö sen ord eftersom den ger en sömlös reserv metod för gäst användare. Men du kan inaktivera den här funktionen om du väljer att inte använda den. Mer information finns i [e-mail Authentication eng ång slö sen ord](one-time-passcode.md)


## <a name="b2b-recommendations"></a>B2B-rekommendationer
| Rekommendation | Kommentarer |
| --- | --- |
| För en optimal inloggnings upplevelse kan du federera med identitets leverantörer | När det är möjligt kan du federera direkt med identitets leverantörer så att inbjudna användare kan logga in på dina delade appar och resurser utan att behöva skapa Microsoft-konton (MSA: er) eller Azure AD-konton. Du kan använda [Google Federation-funktionen](google-federation.md) för att tillåta B2B-gäst användare att logga in med sina Google-konton. Du kan också använda [funktionen direkt Federation (förhands granskning)](direct-federation.md) för att konfigurera direkt Federation med en organisation vars identitetsprovider (IdP) stöder SAML 2,0 eller WS-Fed-protokollet. |
| Använd funktionen för eng ång slö sen ord för B2B-gäster som inte kan autentisera på annat sätt | Med funktionen för [eng ång slö sen ord](one-time-passcode.md) autentiseras B2B-gäst användare när de inte kan autentiseras via andra sätt som Azure AD, en Microsoft-konto (MSA) eller Google Federation. När gäst användaren löser in en inbjudan eller får åtkomst till en delad resurs, kan de begära en tillfällig kod som skickas till deras e-postadress. Sedan anger de den här koden för att fortsätta logga in. |
| Lägg till företagsanpassning på inloggningssidan | Du kan anpassa inloggnings sidan så att det blir mer intuitivt för dina B2B-gäst användare. Se så här [lägger du till företags anpassning för inloggnings-och åtkomst panel sidor](../fundamentals/customize-branding.md). |
| Lägg till din sekretess policy till den B2B-gäst användaren inlösnings upplevelse | Du kan lägga till URL: en för din organisations sekretess policy till den första gången inlösnings processen för inbjudan så att en inbjuden användare måste godkänna dina sekretess villkor för att kunna fortsätta. Se [anvisningar: Lägg till din organisations sekretess information i Azure Active Directory](../fundamentals/active-directory-properties-area.md). |
| Använd funktionen för Mass inbjudan (för hands version) för att bjuda in flera B2B-gäst användare på samma tid | Bjud in flera gäst användare till din organisation samtidigt med hjälp av funktionen för förhands granskning av Mass inbjudan i Azure Portal. Med den här funktionen kan du ladda upp en CSV-fil för att skapa B2B-gäst användare och skicka inbjudningar i bulk. Mer information finns i [självstudier för Mass inbjudan B2B-användare](tutorial-bulk-invite.md). |
| Tillämpa principer för villkorlig åtkomst för Multi-Factor Authentication (MFA) | Vi rekommenderar att du tillämpar MFA-principer på de appar som du vill dela med partners B2B-användare. På så sätt tillämpas MFA konsekvent på apparna i din klient, oavsett om partner organisationen använder MFA. Se [villkorlig åtkomst för B2B-samarbets användare](conditional-access.md). |
| Om du tillämpar enhets principer för villkorlig åtkomst använder du undantags listor för att ge åtkomst till B2B-användare | Om enhetbaserade principer för villkorlig åtkomst har Aktiver ATS i din organisation blockeras B2B-gäst användar enheter eftersom de inte hanteras av din organisation. Du kan skapa undantags listor som innehåller vissa partner användare för att undanta dem från den enhets principen för villkorlig åtkomst. Se [villkorlig åtkomst för B2B-samarbets användare](conditional-access.md). |
| Använd en klient-/regionsspecifika URL när du tillhandahåller direkta länkar till dina B2B-gäst användare | Som ett alternativ till e-postinbjudan kan du ge en gäst en direkt länk till din app eller Portal. Den här direkta länken måste vara klient-/regionsspecifika, vilket innebär att den måste innehålla ett klient-ID eller en verifierad domän så att gästen kan autentiseras i din klient, där den delade appen finns. Se [inlösnings upplevelse för gäst användaren](redemption-experience.md). |
| När du utvecklar en app använder du UserType för att fastställa användar upplevelsen för gästen  | Om du utvecklar ett program och vill tillhandahålla olika upplevelser för klient användare och gäst användare använder du UserType-egenskapen. UserType-anspråket ingår för närvarande inte i token. Program ska använda Microsoft Graph-API: et för att söka efter användarens UserType i katalogen. |
| Ändra egenskapen UserType *endast* om användarens relation till organisationen ändras | Även om det går att använda PowerShell för att konvertera UserType-egenskapen för en användare från medlem till gäst (och vice versa) bör du ändra den här egenskapen endast om relationen mellan användaren och din organisation ändras. Se [egenskaperna för en B2B-gäst användare](user-properties.md).|

## <a name="next-steps"></a>Nästa steg

[Hantera B2B-delning](delegate-invitations.md)