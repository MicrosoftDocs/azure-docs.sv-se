---
title: Bygg in landnings sidan för ditt SaaS-erbjudande i kommersiellt marknads plats
description: Lär dig hur du skapar en landnings sida för ditt SaaS-erbjudande.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 4bfc29472373a53bcebb2ba59134d1f3702d4793
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549880"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>Bygg in landnings sidan för ditt SaaS-erbjudande i kommersiellt marknads plats

Den här artikeln vägleder dig genom processen att skapa en landnings sida för en SaaS-app som kommer att säljas på Microsofts kommersiella marknads plats.

## <a name="overview"></a>Översikt

Du kan tänka på landnings sidan som "lobbyn" för ditt SaaS-erbjudande (Software as a Service). När köparen prenumererar på ett erbjudande dirigerar den kommersiella Marketplace dem till landnings sidan för att aktivera och konfigurera prenumerationen på ditt SaaS-program. Tänk på detta som ett order bekräftelse steg som gör det möjligt för köparen att se vad de har köpt och bekräfta sina konto uppgifter. Med hjälp av Azure Active Directory (Azure AD) och Microsoft Graph kan du aktivera enkel inloggning (SSO) för köparen och få viktig information om köparen som du kan använda för att bekräfta och aktivera prenumerationen, inklusive namn, e-postadress och organisation.

Eftersom den information som krävs för att aktivera prenumerationen är begränsad och tillhandahålls av Azure AD och Microsoft Graph, bör det inte finnas något behov av att begära information som kräver mer än grundläggande medgivande. Om du behöver användar information som kräver ytterligare medgivande för ditt program bör du begära den här informationen när prenumerations aktiveringen har slutförts. Detta möjliggör friktions löst prenumerations aktivering för köparen och minskar risken för att överges.

Landnings sidan innehåller vanligt vis följande:

- Presentera namnet på erbjudandet och inköpet, samt fakturerings villkoren.
- Presentera konto informationen för köparen, inklusive för-och efter namn, organisation och e-post.
- Fråga köparen om att bekräfta eller ersätta annan konto information.
- Vägleder köparen vid nästa steg efter aktiveringen. Du kan till exempel få ett välkomst meddelande, hantera prenumerationen, få support eller läsa dokumentation.

> [!NOTE]
> Köparen dirigeras också till landnings sidan när den hanterar prenumerationen efter aktiveringen. När köp prenumerationen har Aktiver ATS måste du använda SSO för att användaren ska kunna logga in. Vi rekommenderar att du dirigerar användaren till en konto profil eller konfigurations sida.

I följande avsnitt får du hjälp med processen att skapa en landnings sida:

1. [Skapa en Azure AD-App-registrering](#create-an-azure-ad-app-registration) för landnings sidan.
1. [Använd ett kod exempel som utgångs punkt](#use-a-code-sample-as-a-starting-point) för din app.
1. [Använd två Azure AD-appar för att förbättra säkerheten i produktionen](#use-two-azure-ad-apps-to-improve-security-in-production).
1. [Lös identifierings-token för Marketplace-inköp](#resolve-the-marketplace-purchase-identification-token) som lagts till URL: en av den kommersiella Marketplace.
1. [Läs information från anspråk som kodats i ID-token](#read-information-from-claims-encoded-in-the-id-token), som togs emot från Azure AD efter inloggning, som skickades med begäran.
1. [Använd Microsoft Graph API](#use-the-microsoft-graph-api) för att samla in ytterligare information, efter behov.

## <a name="create-an-azure-ad-app-registration"></a>Skapa en Azure AD-App-registrering

Den kommersiella marknads platsen är helt integrerad med Azure AD. Köpare kommer till Marketplace som autentiserats med ett [Azure AD-konto eller Microsoft-konto (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Efter köpet går köparen från den kommersiella marknads platsen till din landnings sidas URL för att aktivera och hantera prenumerationen på ditt SaaS-program. Du måste låta köparen logga in till ditt program med Azure AD SSO. (Landnings sidans URL anges på erbjudandets [tekniska konfigurations](plan-saas-offer.md#technical-information) sida.

Det första steget för att använda identiteten är att se till att din landnings sida registreras som ett Azure AD-program. Genom att registrera programmet kan du använda Azure AD för att autentisera användare och begära åtkomst till användar resurser. Det kan anses vara programmets definition, som gör att tjänsten vet hur token ska utfärdas till appen baserat på appens inställningar.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registrera ett nytt program med hjälp av Azure-portalen

Kom igång genom att följa anvisningarna för att [Registrera ett nytt program](../active-directory/develop/quickstart-register-app.md). Om du vill låta användare från andra företag besöka appen måste du välja ett av alternativen för flera klient organisationer när du tillfrågas om vem som kan använda programmet.

Om du tänker fråga Microsoft Graph-API: t [konfigurerar du ditt nya program för att få åtkomst till webb-API: er](../active-directory/develop/quickstart-configure-app-access-web-apis.md). När du väljer API-behörigheter för det här programmet är standardvärdet för **User. Read** tillräckligt för att samla in grundläggande information om köparen för att göra onboarding-processen smidig och automatisk. Begär inte några API-behörigheter som är märkta **kräver administratörs medgivande**, eftersom detta hindrar alla användare som inte är administratörer att besöka din landnings sida.

Om du behöver utökade behörigheter som en del av din onboarding-eller etablerings process bör du överväga att använda de [stegvisa medgivande](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) funktionerna i Azure AD så att alla köpare som skickas från Marketplace kan interagera från början med landnings sidan.

## <a name="use-a-code-sample-as-a-starting-point"></a>Använda ett kod exempel som utgångs punkt

Vi har tillhandahållit flera exempel på appar som implementerar en enkel webbplats där Azure AD-inloggning är aktiverat. När programmet har registrerats i Azure AD erbjuder **snabb starts** bladet en lista över vanliga program typer och utvecklings stackar som visas i bild 1. Välj den som matchar din miljö och följ anvisningarna för hämtning och installation.

***Bild 1: snabb starts bladet i Azure Portal***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Visar bladet snabb start i Azure Portal.":::

När du har laddat ned koden och konfigurerat din utvecklings miljö ändrar du konfigurations inställningarna i appen så att de återspeglar program-ID, klient-ID och klient hemlighet som du registrerade i föregående procedur. Observera att de exakta stegen varierar beroende på vilket exempel du använder.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>Använd två Azure AD-appar för att förbättra säkerheten i produktion

Den här artikeln innehåller en förenklad version av arkitekturen för att implementera en landnings sida för ditt kommersiella Marketplace SaaS-erbjudande. När du kör sidan i produktion rekommenderar vi att du förbättrar säkerheten genom att kommunicera med API: er för SaaS-utförande endast via ett annat, säkert program. Detta kräver att du skapar två nya program:

- Först beskrivits sidan för landnings sidan för flera innehavare, förutom om funktionen inte kontaktar SaaS-API: er. Den här funktionen kommer att avlastas till ett annat program, enligt beskrivningen nedan.
- Det andra är ett program för att äga kommunikationen med API: er för SaaS-utförande. Det här programmet bör vara en enda klient organisation, som endast används av din organisation, och en åtkomst kontrol lista kan upprättas för att begränsa åtkomsten till API: erna från den här appen.

Detta gör det möjligt för lösningen att fungera i scenarier som ser till att problemet [skiljs från betänkligheter](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) . Landnings sidan använder till exempel den första registrerade Azure AD-appen för att logga in användaren. När användaren är inloggad använder landnings sidan den andra Azure AD för att begära en åtkomsttoken för att anropa API: et för SaaS-uppfyllelse och anropa åtgärden lös.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>Lös identifierings-token för Marketplace-inköp

När köparen skickas till landnings sidan läggs en token till i URL-parametern. Den här token skiljer sig från både Azure AD-utfärdade token och åtkomsttoken som används för tjänst-till-tjänst-autentisering och används som indata för [API: erna för SaaS-uppfyllande](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) anrop för att hämta information om prenumerationen. Precis som med alla anrop till SaaS-API: er, autentiseras din tjänst-till-tjänst-begäran med en åtkomsttoken som baseras på appens Azure AD Application ID-användare för tjänst-till-tjänst-autentisering.

> [!NOTE]
> I de flesta fall är det bättre att göra det här anropet från ett andra program med en enda klient. Se [använda två Azure AD-appar för att förbättra säkerheten i produktionen](#use-two-azure-ad-apps-to-improve-security-in-production) tidigare i den här artikeln.

### <a name="request-an-access-token"></a>Begära åtkomsttoken

Om du vill autentisera ditt program med SaaS-API: er behöver du en åtkomsttoken som kan genereras genom att anropa Azure AD OAuth-slutpunkten. Se [hur du hämtar utgivarens](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)autentiseringstoken.

### <a name="call-the-resolve-endpoint"></a>Anropa matchnings slut punkten

API: erna för SaaS-utförande implementerar den [lösa](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) slut punkten som kan anropas för att bekräfta giltigheten hos Marketplace-token och för att returnera information om prenumerationen.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Läsa information från anspråk som är kodade i ID-token

Som en del av [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) -flödet lägger Azure AD till en [ID-token](../active-directory/develop/id-tokens.md) till begäran när köparen skickas till landnings sidan. Denna token innehåller flera delar av grundläggande information som kan vara användbar vid aktiverings processen, inklusive den information som visas i den här tabellen.

| Värde | Beskrivning |
| ------------ | ------------- |
| aud | Avsedd mål grupp för denna token. I det här fallet ska det matcha ditt program-ID och verifieras. |
| preferred_username | Primärt användar namn för den besökta användaren. Detta kan vara en e-postadress, ett telefonnummer eller en annan identifierare. |
| e-post | Användarens e-postadress. Observera att det här fältet kan vara tomt. |
| name | Människo-läsbart värde som identifierar ämnet för token. I det här fallet är det köparens namn. |
| OID | Identifierare i Microsoft Identity-systemet som unikt identifierar användaren i olika program. Microsoft Graph returnerar detta värde som ID-egenskap för ett angivet användar konto. |
| bekräfta | Identifierare som representerar den Azure AD-klient som köparen är från. Om det gäller en MSA-identitet är detta alltid ``9188040d-6c67-4c5b-b112-36a304b66dad`` . Mer information finns i kommentaren i nästa avsnitt: Använd Microsoft Graph-API: et. |
| Build | Identifierare som unikt identifierar användaren i det här specifika programmet. |
|||

## <a name="use-the-microsoft-graph-api"></a>Använda Microsoft Graph API

ID-token innehåller grundläggande information för att identifiera köparen, men din aktiverings process kan kräva ytterligare information, t. ex. köparens företag, för att slutföra onboarding-processen. Använd [Microsoft Graph-API: et](/graph/use-the-api) för att begära den här informationen för att undvika att användaren kan ange informationen igen. Standard **användaren. Läs** behörighet innehåller följande information, som standard.

| Värde | Beskrivning |
| ------------ | ------------- |
| displayName | Namn som visas i användarens adress bok. |
| förnamn | Användarens förnamn. |
| jobTitle | Användarens jobb titel. |
| e-post | Användarens SMTP-adress. |
| mobilePhone | Primärt mobilt mobiltelefon nummer för användaren. |
| preferredLanguage | ISO 639-1-kod för användarens önskade språk. |
| surname | Användarens efter namn. |
|||

Ytterligare egenskaper, till exempel namnet på användarens företag eller användarens plats (land), kan väljas för att inkluderas i begäran. Mer information finns i [Egenskaper för användar resurs typen](/graph/api/resources/user#properties) .

De flesta appar som är registrerade med Azure AD tilldelar delegerade behörigheter för att läsa användarens information från företagets Azure AD-klient. Alla begär Anden om att Microsoft Graph för den informationen måste åtföljas av en åtkomsttoken för autentisering. De olika stegen för att generera åtkomsttoken beror på vilken teknik stack du använder, men exempel koden innehåller ett exempel. Mer information finns i [få åtkomst för en användares räkning](/graph/auth-v2-user).

> [!NOTE]
> Konton från MSA-klienten (med klient-ID ``9188040d-6c67-4c5b-b112-36a304b66dad`` ) returnerar inte mer information än vad som redan har samlats in med ID-token. Så du kan hoppa över det här anropet till Graph API för dessa konton.

## <a name="next-steps"></a>Nästa steg

- [Så här skapar du ett SaaS-erbjudande på den kommersiella Marketplace](create-new-saas-offer.md)