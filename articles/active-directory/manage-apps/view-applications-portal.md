---
title: 'Snabbstart: Visa listan över program som använder din Azure Active Directory (Azure AD)-klient för identitetshantering'
description: I den här snabbstarten använder du Azure Portal för att visa listan över program som har registrerats för att använda din Azure Active Directory-klientorganisation (Azure AD) för identitetshantering.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: iangithinji
ms.reviewer: arvinh
ms.custom: it-pro
ms.openlocfilehash: c4a03e85798d4406068bd5923dbc753d8e5d31b6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376451"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Snabbstart: Visa listan över program som använder din Azure Active Directory (Azure AD)-klient för identitetshantering

Kom igång med Azure AD som identitets- och åtkomsthanteringssystem (IAM) för de program som din organisation använder. I den här snabbstarten visar du de program, även kallade appar, som redan har ställts in för att använda din Azure AD-klient som identitetsprovider (IdP).

## <a name="prerequisites"></a>Förutsättningar

Om du vill visa program som har registrerats i din Azure AD-klientorganisation behöver du:

- Ett Azure-konto. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

>[!IMPORTANT]
>Vi rekommenderar att du använder en icke-produktionsmiljö för att testa stegen i den här snabbstarten.

## <a name="find-the-list-of-applications-in-your-tenant"></a>Hitta listan över program i din klientorganisation

De program som är registrerade i din Azure AD-klientorganisation kan visas i **avsnittet Enterprise-appar** i Azure Portal.

Så här visar du de program som är registrerade i din klientorganisation:

1. Logga in på din [Azure Portal](https://portal.azure.com).
2. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**.
3. I fönstret **Azure Active Directory** väljer du **Företagsprogram**.
4. I **listrutan Programtyp** väljer du **Alla program** och sedan **Använd.** Ett slumpmässigt urval av programmen i din klientorganisation visas.
5. Om du vill visa fler program **väljer du Läs** in mer längst ned i listan. Om det finns flera program i din klientorganisation kan det vara enklare att söka efter ett visst program i stället för att bläddra i listan. Sökning efter ett visst program tas upp senare i den här snabbstarten.

## <a name="select-viewing-options"></a>Välj visningsalternativ

Välj alternativ beroende på vad du letar efter.

1. Du kan visa programmen efter **Programtyp,** **Programstatus** och **Programsynlighet.**
2. Välj något av följande alternativ under **Programtyp**:
    - **Företagsprogram** visar andra program än Microsoft-program.
    - **Microsoft-program** visar Microsoft-program.
    - **Alla program** visar både Microsoft-program och andra program.
3. Välj **Alla**, **Inaktiverade** eller **Aktiverade** under **Programstatus**. Alternativet **Alla** omfattar både inaktiverade och aktiverade program.
4. Välj **Alla** eller **Dolda** under **Synlighet för program**. Alternativet **Dold** visar program som finns i klientorganisationen, men som inte är synliga för användarna.
5. När du har valt de alternativ som du vill ha väljer du **Använd**.

## <a name="search-for-an-application"></a>Söka efter ett program

Så här söker du efter ett visst program:

1. På menyn **Programtyp** väljer du **Alla program** och sedan **Använd**.
2. Ange namnet på det program som du vill söka efter. Om programmet har lagts till i din Azure AD-klientorganisation visas det i sökresultaten. Det här exemplet visar att GitHub inte har lagts till i klientprogrammen.
    ![Exempel som visar att en app inte har lagts till i klientorganisationen](media/view-applications-portal/search-for-tenant-application.png)
3. Prova att skriva de första bokstäverna i ett programnamn. I det här exemplet visas alla program som börjar med **Sales**.
    ![Exempel som visar alla appar som börjar med Försäljning](media/view-applications-portal/search-by-prefix.png)


> [!TIP]
> Du kan automatisera apphantering med hjälp av Graph API i [Automatisera apphantering med Microsoft Graph API.](/graph/application-saml-sso-configure-api)


## <a name="clean-up-resources"></a>Rensa resurser

Du har inte skapat några nya resurser i den här snabbstarten, så det finns inget att rensa.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du använder Azure AD som identitetsprovider för en app.
> [!div class="nextstepaction"]
> [Lägga till en app](add-application-portal.md)