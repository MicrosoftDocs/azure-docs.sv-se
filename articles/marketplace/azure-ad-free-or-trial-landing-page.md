---
title: Bygg in landnings sidan för ditt kostnads fria eller utvärderings SaaS-erbjudande på den kommersiella Marketplace
description: Lär dig hur du skapar en landnings sida för ditt kostnads fria eller utvärderings SaaS-erbjudande.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: e7cee47e90e6484a4258ba82e47af03725c41d34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559298"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>Bygg in landnings sidan för ditt kostnads fria eller utvärderings SaaS-erbjudande på den kommersiella Marketplace

Den här artikeln vägleder dig genom processen att skapa en landnings sida för en kostnads fri eller utvärderings version av SaaS som kommer att säljas på Microsofts kommersiella marknads plats.

## <a name="overview"></a>Översikt

Du kan tänka på landnings sidan som "lobbyn" för ditt SaaS-erbjudande (Software as a Service). När kunden har valt att hämta din app dirigerar den kommersiella Marketplace dem till landnings sidan för att aktivera och konfigurera prenumerationen på ditt SaaS-program. När du skapar ett SaaS-erbjudande (Software as a Service) kan du välja om du vill [sälja via Microsoft](plan-saas-offer.md#listing-options)i Partner Center. Om du bara vill lista ditt erbjudande på Microsofts kommersiella marknads plats och inte säljer via Microsoft kan du ange hur potentiella kunder kan interagera med erbjudandet. När du aktiverar List rutan **Hämta den nu (kostnads fri)** eller den **kostnads fria utvärderings versionen** måste du ange en webb adress till landnings sidan som användaren kan gå till för att få åtkomst till den kostnads fria prenumerationen eller utvärderings versionen.

Syftet med landnings sidan är att bara ta emot användaren så att de kan aktivera den kostnads fria utvärderings versionen eller prenumerationen. Med hjälp av Azure Active Directory (Azure AD) och Microsoft Graph kan du aktivera enkel inloggning (SSO) för användaren och få viktig information om användaren som du kan använda för att aktivera en kostnads fri utvärderings version eller en kostnads fri prenumeration, inklusive namn, e-postadress och organisation.

Eftersom den information som krävs för att aktivera prenumerationen är begränsad och tillhandahålls av Azure AD och Microsoft Graph, bör det inte finnas något behov av att begära information som kräver mer än grundläggande medgivande. Om du behöver användar information som kräver ytterligare medgivande för ditt program bör du begära den här informationen när prenumerations aktiveringen har slutförts. Detta möjliggör friktions löst prenumerations aktivering för användaren och minskar risken för att överges.

Landnings sidan innehåller vanligt vis följande information och list alternativ:

- Presentera namn och information om den kostnads fria utvärderings versionen eller prenumerationen. Ange till exempel användnings gränser eller varaktighet för en utvärderings version.
- Presentera användarens konto uppgifter, inklusive för-och efter namn, organisation och e-post.
- Fråga användaren om de vill bekräfta eller ersätta andra konto uppgifter.
- Vägleder användaren vid nästa steg efter aktiveringen. Du kan till exempel få ett välkomst meddelande, hantera prenumerationen, få support eller läsa dokumentation.

Följande avsnitt i den här artikeln vägleder dig genom processen för att skapa en landnings sida:

1. [Skapa en Azure AD-App-registrering](#create-an-azure-ad-app-registration) för landnings sidan.
2. [Använd ett kod exempel som utgångs punkt](#use-a-code-sample-as-a-starting-point) för din app.
3. [Läs information från anspråk som är kodade i ID-token](#read-information-from-claims-encoded-in-the-id-token), som tagits emot från Azure AD efter inloggning, som skickades med begäran.
4. [Använd Microsoft Graph API](#use-the-microsoft-graph-api) för att samla in ytterligare information, efter behov.

## <a name="create-an-azure-ad-app-registration"></a>Skapa en Azure AD-App-registrering

Den kommersiella marknads platsen är helt integrerad med Azure AD. Användare som kommer till Marketplace autentiseras med ett [Azure AD-konto eller Microsoft-konto (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology). När du har skaffat en kostnads fri eller kostnads fri utvärderings prenumeration via listan med endast ett erbjudande, går användaren från den kommersiella Marketplace till din landnings sidas URL för att aktivera och hantera prenumerationen på SaaS-programmet. Du måste låta användaren logga in till ditt program med Azure AD SSO. (Landnings sidans URL anges på erbjudandets [tekniska konfigurations](plan-saas-offer.md#technical-information) sida.

Det första steget för att använda identiteten är att se till att din landnings sida registreras som ett Azure AD-program. Genom att registrera programmet kan du använda Azure AD för att autentisera användare och begära åtkomst till användar resurser. Det kan anses vara programmets definition, som gör att tjänsten vet hur token ska utfärdas till appen baserat på appens inställningar.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registrera ett nytt program med hjälp av Azure-portalen

Kom igång genom att följa anvisningarna för att [Registrera ett nytt program](../active-directory/develop/quickstart-register-app.md). Om du vill låta användare från andra företag besöka appen måste du välja **konton i valfri organisations katalog (alla Azure AD-kataloger – flera innehavare) och personliga Microsoft-konton (t. ex. Skype eller Xbox)** när de uppmanas att använda programmet.

Om du tänker fråga Microsoft Graph-API: t [konfigurerar du ditt nya program för att få åtkomst till webb-API: er](../active-directory/develop/quickstart-configure-app-access-web-apis.md). När du väljer API-behörigheter för det här programmet är standardvärdet **User. Read** tillräckligt för att samla in grundläggande information om användaren för att göra onboarding-processen smidig och automatisk. Begär inte några API-behörigheter som är märkta **kräver administratörs medgivande**, eftersom detta hindrar alla användare som inte är administratörer att besöka din landnings sida.

Om du behöver utökade behörigheter som en del av din onboarding-eller etablerings process bör du överväga att använda de [stegvisa medgivande](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) funktionerna i Azure AD så att alla användare som skickas från Marketplace kan interagera från början med landnings sidan.

## <a name="use-a-code-sample-as-a-starting-point"></a>Använda ett kod exempel som utgångs punkt

Microsoft tillhandahåller flera exempel på appar som implementerar en enkel webbplats där Azure AD-inloggning är aktive rad. När programmet har registrerats i Azure AD erbjuder **snabb starts** bladet en lista över vanliga program typer och utvecklings stackar (bild 1). Välj den som matchar din miljö och följ anvisningarna för hämtning och installation.

***Bild 1: snabb starts bladet i Azure Portal***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Visar bladet snabb start i Azure Portal.":::

När du har laddat ned koden och konfigurerat din utvecklings miljö ändrar du konfigurations inställningarna i appen så att de återspeglar program-ID, klient-ID och klient hemlighet som du registrerade i föregående procedur. Observera att de exakta stegen varierar beroende på vilket exempel du använder.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Läsa information från anspråk som är kodade i ID-token

Som en del av [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) -flödet lägger Azure AD till en [ID-token](../active-directory/develop/id-tokens.md) i begäran när användaren skickas till landnings sidan. Denna token innehåller flera delar av grundläggande information som kan vara användbar vid aktiverings processen, inklusive den information som visas i den här tabellen.

| Värde | Beskrivning |
| ------------ | ------------- |
| aud | Avsedd mål grupp för denna token. I det här fallet ska det matcha ditt program-ID och verifieras. |
| preferred_username | Primärt användar namn för den besökta användaren. Detta kan vara en e-postadress, ett telefonnummer eller en annan identifierare. |
| e-post | Användarens e-postadress. Observera att det här fältet kan vara tomt. |
| name | Människo-läsbart värde som identifierar ämnet för token. I det här fallet är det användarens namn. |
| OID | Identifierare i Microsoft Identity-systemet som unikt identifierar användaren i olika program. Microsoft Graph returnerar detta värde som ID-egenskap för ett angivet användar konto. |
| bekräfta | Identifierare som representerar den Azure AD-klient som användaren är från. Om det gäller en MSA-identitet är detta alltid `9188040d-6c67-4c5b-b112-36a304b66dad` . Mer information finns i kommentaren i nästa avsnitt: Använd Microsoft Graph-API. |
| Build | Identifierare som unikt identifierar användaren i det här specifika programmet. |
|||

## <a name="use-the-microsoft-graph-api"></a>Använda Microsoft Graph API

ID-token innehåller grundläggande information för att identifiera användaren, men din aktiverings process kan kräva ytterligare information, t. ex. användarens företag, för att slutföra onboarding-processen. Använd [Microsoft Graph-API: et](/graph/use-the-api) för att begära den här informationen för att undvika att användaren kan ange informationen igen. Standard **användaren. Läs** behörighet innehåller följande information, som standard:

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

Ytterligare egenskaper, till exempel namnet på användarens företag eller användarens plats (land), kan väljas för att inkluderas i begäran. Mer information finns i [Egenskaper för användar resurs typen](/graph/api/resources/user#properties).

De flesta appar som är registrerade med Azure AD tilldelar delegerade behörigheter för att läsa användarens information från företagets Azure AD-klient. Alla begär Anden om att Microsoft Graph för den informationen måste åtföljas av en åtkomsttoken som autentisering. De olika stegen för att generera åtkomsttoken beror på vilken teknik stack du använder, men exempel koden innehåller ett exempel. Mer information finns i [få åtkomst för en användares räkning](/graph/auth-v2-user).

> [!NOTE]
> Konton från MSA-klienten (med klient-ID `9188040d-6c67-4c5b-b112-36a304b66dad` ) returnerar inte mer information än vad som redan har samlats in med ID-token. Så du kan hoppa över det här anropet till Graph API för dessa konton.

## <a name="next-steps"></a>Nästa steg
- [Så här skapar du ett SaaS-erbjudande på den kommersiella Marketplace](create-new-saas-offer.md)