---
title: Begränsa Azure AD-appen till en uppsättning användare | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du begränsar åtkomsten till dina appar som är registrerade i Azure AD till en vald uppsättning användare.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 71cfe08da42b8eec9ddbd0e4246ba1b72f895414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199586"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Så här: begränsa din Azure AD-App till en uppsättning användare i en Azure AD-klient

Program som är registrerade i en Azure Active Directory-klient (Azure AD) är som standard tillgängliga för alla användare av klienten som autentiseras korrekt.

På samma sätt kommer alla användare i Azure AD-klienten där den här appen är etablerad att kunna komma åt det här programmet när de har autentiserat sig i deras respektive klient organisationer, om det finns en app [med flera klienter](howto-convert-app-to-be-multi-tenant.md) .

Klient organisations administratörer och utvecklare har ofta krav på när en app måste begränsas till en viss uppsättning användare. Utvecklare kan utföra samma användnings mönster med hjälp av populära behörighets mönster som rollbaserad åtkomst kontroll i Azure (Azure RBAC), men den här metoden kräver en stor mängd arbete på en del av utvecklaren.

Klient organisations administratörer och utvecklare kan begränsa en app till en särskild uppsättning användare eller säkerhets grupper i klient organisationen genom att använda den här inbyggda funktionen i Azure AD också.

## <a name="supported-app-configurations"></a>App-konfigurationer som stöds

Alternativet att begränsa en app till en speciell uppsättning användare eller säkerhets grupper i en klient organisation fungerar med följande typer av program:

- Program som kon figurer ATS för federerad enkel inloggning med SAML-baserad autentisering.
- Application Proxy-program som använder Azure AD förautentisering.
- Program som byggts direkt på den Azure AD-programplattform som använder OAuth 2.0/OpenID Connect-autentisering när en användare eller administratör har samtyckt till det programmet.

     > [!NOTE]
     > Den här funktionen är endast tillgänglig för webbapp/webb-API och företags program. Appar som är registrerade som [interna](./quickstart-register-app.md) kan inte begränsas till en uppsättning användare eller säkerhets grupper i klienten.

## <a name="update-the-app-to-enable-user-assignment"></a>Uppdatera appen för att aktivera användar tilldelning

Det finns två sätt att skapa ett program med aktive rad användar tilldelning. En kräver rollen **Global administratör** , den andra inte.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Företags program (kräver rollen som global administratör)

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> som **Global administratör**.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **företags program**  >  **alla program**.
1. Välj det program som du vill tilldela en användare eller en säkerhets grupp till i listan. 
    Använd filtren längst upp i fönstret för att söka efter ett angivet program.
1. På sidan **Översikt** för program under **Hantera** väljer du **Egenskaper**.
1. Leta upp inställningen **användar tilldelning krävs?** och ange den till **Ja**. När det här alternativet är inställt på **Ja** måste användare i klient organisationen först tilldelas till det här programmet, annars kan de inte logga in i programmet.
1. Välj **Spara**.

### <a name="app-registration"></a>Appregistrering

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Välj **Appregistreringar** under **Hantera**.
1. Skapa eller Välj den app som du vill hantera. Du måste vara **ägare** till det här programmet.
1. På sidan **Översikt** för program väljer du länken **hanterat program i lokal katalog** i avsnittet **Essentials** .
1. Välj **Egenskaper** under **Hantera**.
1. Leta upp inställningen **användar tilldelning krävs?** och ange den till **Ja**. När det här alternativet är inställt på **Ja** måste användare i klient organisationen först tilldelas till det här programmet, annars kan de inte logga in i programmet.
1. Välj **Spara**.

## <a name="assign-users-and-groups-to-the-app"></a>Tilldela användare och grupper till appen

När du har konfigurerat din app för att aktivera användar tilldelning kan du gå vidare och tilldela användare och grupper till appen.

1. Under **Hantera** väljer du **användare och grupper**  >  **Lägg till användare/grupp** .
1. Välj **användarens** väljare. 

     En lista över användare och säkerhets grupper visas tillsammans med en text ruta där du kan söka efter och hitta en viss användare eller grupp. På den här skärmen kan du välja flera användare och grupper i en enda go.

1. När du är färdig med att välja användare och grupper väljer du **Välj**.
1. Valfritt Om du har definierat app-roller i programmet kan du använda alternativet **Välj roll** för att tilldela de valda användarna och grupperna till en av programmets roller. 
1. Välj **tilldela** för att slutföra tilldelningen av användare och grupper till appen. 
1. Bekräfta att de användare och grupper som du har lagt till visas i listan uppdaterade **användare och grupper** .

## <a name="more-information"></a>Mer information

- [Gör så här: Lägg till app-roller i ditt program](./howto-add-app-roles-in-azure-ad-apps.md)
- [Lägg till auktorisering med hjälp av app-roller & roller anspråk till en ASP.NET Core webbapp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Använda säkerhets grupper och program roller i dina appar (video)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory, nu med grupp anspråk och program roller](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory-appmanifest](./reference-app-manifest.md)
