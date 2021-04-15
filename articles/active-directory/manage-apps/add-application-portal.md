---
title: 'Snabbstart: Lägga till ett program i din Azure Active Directory klient (Azure AD)'
description: I den här snabbstarten använder du Azure Portal för att lägga till ett galleriprogram i din Azure Active Directory-klientorganisation (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: iangithinji
ms.openlocfilehash: d7827d6419e4d820f8aca482df2cfae83eaf319d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378898"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Snabbstart: Lägga till ett program i din Azure Active Directory klient (Azure AD)

Azure Active Directory (Azure AD) har ett galleri som innehåller tusentals förintegrerade program. Många av de program som din organisation använder finns förmodligen redan i galleriet.

När ett program har lagts till Azure AD-klienten kan du:

- Konfigurera egenskaper för appen.
- Hantera användaråtkomst till appen med en princip för villkorlig åtkomst.
- Konfigurera enkel inloggning så att användarna kan logga in på appen med sina Autentiseringsuppgifter för Azure AD.

## <a name="prerequisites"></a>Förutsättningar

Om du vill lägga till ett program till din Azure AD-klientorganisation behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- En av följande roller: Global administratör, Molnprogramadministratör, Programadministratör eller ägare av tjänstens huvudnamn.
- (Valfritt: Slutförande [av Visa dina appar](view-applications-portal.md)).

>[!IMPORTANT]
>Vi rekommenderar att du använder en icke-produktionsmiljö för att testa stegen i den här snabbstarten.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Lägga till en app i din Azure AD-klientorganisation

Så här lägger du till ett program i din Azure AD-klientorganisation:

1. I den [Azure Portal](https://portal.azure.com)navigeringspanelen väljer du **Azure Active Directory**.
2. I fönstret **Azure Active Directory** väljer du **Företagsprogram**. Fönstret **Alla program** öppnas och visar ett slumpmässigt urval av programmen i din Azure AD-klientorganisation.
3. I fönstret **Företagsprogram** väljer du **Nytt program**. 
    ![Välj Nytt program för att lägga till en galleriapp i din klientorganisation](media/add-application-portal/new-application.png)
4. Växla till den nya galleriförhandsgranskningen: I banderollen längst upp på sidan Lägg till ett program **väljer** du länken Klicka här för att prova det nya och **förbättrade appgalleriet**.
5. Fönstret **Bläddra i Azure AD-galleriet (förhandsversion)** öppnas och visar paneler för molnplattformar, lokala program och aktuella program. Program som anges **i avsnittet Aktuella** program har ikoner som anger om de stöder federerad enkel inloggning (SSO) och etablering. 
    ![Sök efter en app efter namn eller kategori](media/add-application-portal/browse-gallery.png)
6. Du kan bläddra i galleriet efter det program som du vill lägga till eller söka efter programmet genom att ange dess namn i sökrutan. Välj sedan programmet i resultatet. 
7. Nästa steg beror på hur utvecklaren av programmet implementerade enkel inloggning (SSO). Enkel inloggning kan implementeras av apputvecklare på fyra sätt. De fyra sätten är SAML, OpenID Connect, Password och Linked. När du lägger till en app kan du välja att filtrera och endast se appar som använder en viss implementering av enkel inloggning enligt skärmbilden. Till exempel kallas en populär standard för att implementera enkel inloggning Security Assertion Markup Language (SAML). En annan standard som är populär kallas OpenId Connect (OIDC). Hur du konfigurerar enkel inloggning med dessa standarder skiljer sig åt, så notera vilken typ av enkel inloggning som implementeras av den app som du lägger till.

    :::image type="content" source="media/add-application-portal/sso-types.png" alt-text="Skärmbild som visar väljaren för SSO-typer." lightbox="media/add-application-portal/sso-types.png":::

    - Om apputvecklaren använde **OIDC-standarden för** enkel inloggning väljer du **Registrera dig.** En konfigurationssida visas. Gå sedan till snabbstarten om att konfigurera OIDC-baserad enkel inloggning.
    :::image type="content" source="media/add-application-portal/sign-up-oidc-sso.png" alt-text="Skärmbild som visar hur du lägger till en OIDC-baserad SSO-app.":::

    - Om apputvecklaren använde **SAML-standarden för** enkel inloggning väljer du **Skapa.** En komma igång-sida visas med alternativ för att konfigurera programmet för din organisation. I formuläret kan du redigera namnet på programmet så att det matchar organisationens behov. Gå sedan till snabbstarten om att konfigurera SAML-baserad enkel inloggning.
    :::image type="content" source="media/add-application-portal/create-application.png" alt-text="Skärmbild som visar hur du lägger till en SAML-baserad SSO-app.":::


> [!IMPORTANT]
> Det finns några viktiga skillnader mellan SAML-baserade och OIDC-baserade SSO-implementeringar. Med SAML-baserade appar kan du lägga till flera instanser av samma app. Till exempel GitHub1, GitHub2 osv. För OIDC-baserade appar kan du bara lägga till en instans av en app. Om du redan har lagt till en OIDC-baserad app och försöker lägga till samma app igen och ge medgivande två gånger, läggs den inte till igen i klientorganisationen.

Om det program som du letar efter inte finns  i galleriet kan du välja länken Skapa ett eget program och sedan välja Integrera andra program som du inte hittar i galleriet under Vad vill du göra med **ditt program?**  Microsoft har redan arbetat med många programutvecklare för att förkonfigurera dem så att de fungerar med Azure AD. De förkonfigurerade apparna visas i galleriet. Men om den app som du vill lägga till inte visas kan du skapa en ny, generisk app och sedan konfigurera den själv eller med hjälp av utvecklaren som skapade den.

Du har lagt till ett program. Nästa snabbstart visar hur du ändrar logotypen och redigerar andra egenskaper för ditt program.

> [!TIP]
> Du kan automatisera apphantering med hjälp av Graph API, se [Automatisera apphantering med Microsoft Graph API.](/graph/application-saml-sso-configure-api)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta med snabbstartsserien kan du ta bort appen för att rensa testklienten. Du kan ta bort appen i den senaste snabbstarten i den här serien. Se [Ta bort en app.](delete-application-portal.md)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du konfigurerar en app.
> [!div class="nextstepaction"]
> [Konfigurera en app](add-application-portal-configure.md)