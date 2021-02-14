---
title: Vad är B2B-samarbete i Azure Active Directory?
description: Azure Active Directory B2B-samarbete stöder gästanvändaråtkomst så att du på ett säkert sätt kan dela resurser och samarbeta med externa partner.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e02cb23188b552b1779d184a2384835f4195762
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365386"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Vad är gästanvändaråtkomst i Azure Active Directory B2B?

Azure Active Directory (B2B) samarbete mellan företag (Azure AD) är en funktion i externa identiteter som gör att du kan bjuda in gäst användare att samar beta med din organisation. Med B2B-samarbete kan du på ett säkert sätt dela ditt företags program och tjänster med gäst användare från vilken annan organisation som helst, samtidigt som du behåller kontrollen över dina egna företags data. Arbeta tryggt och säkert med externa partners, stora som små, även om de inte har Azure AD eller någon IT-avdelning. Tack vare en enkel process för inbjudan och inlösen kan dessa partners använda sina egna autentiseringsuppgifter för att få åtkomst till företagets resurser. Utvecklare kan använda Azure AD B2Bs API:er för att anpassa inbjudningsprocessen eller skriva program som självbetjäningsportaler för registrering. Information om licensiering och prissättning som rör gäst användare finns i [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/).  

> [!IMPORTANT]
> - Från och med den **4 januari 2021** är Google [inaktuell WebView-inloggning support](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Om du använder Google Federation eller självbetjänings registrering med Gmail bör du [testa dina verksamhetsbaserade interna program för kompatibilitet](google-federation.md#deprecation-of-webview-sign-in-support).
> - **Från den 2021 oktober** kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbets scenarier. Vi rekommenderar att kunderna väljer [autentisering med e-post med eng ång slö sen ord](one-time-passcode.md). Vi välkomnar din feedback om den här offentliga för hands versionen och är glada att skapa ännu fler sätt att samar beta.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Samarbeta med alla partners med hjälp av deras identiteter

Med Azure AD B2B använder dina partners sina egna lösningar för identitetshantering, vilket innebär att inte tillkommer några externa administrativa kostnader för din organisation. Gästanvändarna loggar in på dina appar och tjänster med sina egna arbets- eller skolidentiteter eller sociala identiteter.

- Dina partners använder sina egna identiteter och autentiseringsuppgifter. Azure AD är inte obligatoriskt.
- Du behöver inte hantera externa konton eller lösenord.
- Du behöver inte synkronisera konton eller hantera kontolivscykler.  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Bjud enkelt in gäst användare från Azure AD-portalen

Som administratör kan du enkelt lägga till gästanvändare för din organisation i Azure Portal.

- [Skapa en ny gäst användare](b2b-quickstart-add-guest-users-portal.md) i Azure AD, på samma sätt som du lägger till en ny användare.
- Tilldela gäst användare till appar eller grupper.
- Skicka en inbjudan via e-post som innehåller en överförings länk eller skicka en direkt länk till en app som du vill dela.

![Skärm bild som visar post sidan ny gäst användar inbjudan](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- Gäst användare följer några enkla [inlösnings steg](redemption-experience.md) för att logga in.

![Skärm bild som visar sidan gransknings behörigheter](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Använda principer för att på ett säkert sätt dela dina appar och tjänster

Du kan använda Auktoriseringsprinciper för att skydda ditt företags innehåll. Principer för villkorlig åtkomst, till exempel Multi-Factor Authentication, kan tillämpas:

- På klientorganisationsnivå.
- På programnivå.
- För specifika gästanvändare för att skydda företagets appar och data.

![Skärm bild som visar alternativet för villkorlig åtkomst](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Låt program och gruppägare hantera sina egna gästanvändare

Du kan delegera hanteringen av gästanvändarna till programägarna, så att de kan lägga till gästanvändare direkt till de program de vill dela, oavsett om det är ett Microsoft-program eller inte.

- Administratörer konfigurerar självbetjäningapp och grupphantering.
- Icke-administratörer använda sina [åtkomstpanelet](https://myapps.microsoft.com) för att lägga till gästanvändare till program eller grupper.

![Skärm bild som visar åtkomst panelen för en gäst användare](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>Anpassa onboarding-upplevelsen för B2B-gäst användare

Ta med dina externa partner på ett sätt som anpassas efter din organisations behov.

- Använd [hantering av Azure AD-rättighet](../governance/entitlement-management-overview.md) för att konfigurera principer som [hanterar åtkomst för externa användare](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).
- Använd [API: er för inbjudan med B2B-samarbete](/graph/api/resources/invitation) för att anpassa dina onboarding-upplevelser.

## <a name="integrate-with-identity-providers"></a>Integrera med identitets leverantörer

Azure AD stöder externa identitets leverantörer som Facebook, Microsoft-konton, Google-eller Enterprise Identity-leverantörer. Du kan konfigurera Federation med identitets leverantörer så att dina externa användare kan logga in med sina befintliga sociala konton eller företags konton i stället för att skapa ett nytt konto för ditt program. Läs mer om identitets leverantörer för externa identiteter.

![Skärm bild som visar sidan med identitets leverantörer](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow-preview"></a>Skapa ett användar flöde för självbetjänings registrering (förhands granskning)

Med ett användar flöde för självbetjänings registrering kan du skapa en registrerings miljö för externa användare som vill ha åtkomst till dina appar. Som en del av registrerings flödet kan du ange alternativ för olika sociala eller företags identitets leverantörer och samla in information om användaren. Lär dig om självbetjänings [registrering och hur du konfigurerar det](self-service-sign-up-overview.md).

Du kan också använda [API-kopplingar](api-connectors-overview.md) för att integrera dina självbetjänings registrerings användar flöden med externa moln system. Du kan ansluta med anpassade arbets flöden för godkännande, utföra identitets verifiering, validera information från användare och mycket annat.

![Skärm bild som visar sidan användar flöden](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Nästa steg

- [Prissättning för Externa identiteter](external-identities-pricing.md)
- [Lägg till gästanvändare för B2B-samarbete i portalen](add-users-administrator.md)
- [Förstå inlösningsprocessen för inbjudningar](redemption-experience.md)