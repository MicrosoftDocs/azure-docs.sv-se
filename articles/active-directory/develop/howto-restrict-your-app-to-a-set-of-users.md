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
ms.openlocfilehash: f5a5242cb9448b3d11e0921b2272cf00bef8f6c1
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722274"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Så här: begränsa din Azure AD-App till en uppsättning användare i en Azure AD-klient

Program som är registrerade i en Azure Active Directory-klient (Azure AD) är som standard tillgängliga för alla användare av klienten som autentiseras korrekt.

På samma sätt kommer alla användare i Azure AD-klienten där den här appen är etablerad att kunna komma åt det här programmet när de har autentiserat sig i deras respektive klient organisationer, om det finns en app [med flera klienter](howto-convert-app-to-be-multi-tenant.md) .

Klient organisations administratörer och utvecklare har ofta krav på när en app måste begränsas till en viss uppsättning användare. Utvecklare kan utföra samma användnings mönster med hjälp av populära behörighets mönster som rollbaserad åtkomst kontroll i Azure (Azure RBAC), men den här metoden kräver en stor mängd arbete på en del av utvecklaren.

Klient organisations administratörer och utvecklare kan begränsa en app till en särskild uppsättning användare eller säkerhets grupper i klient organisationen genom att använda den här inbyggda funktionen i Azure AD också.

## <a name="supported-app-configurations"></a>App-konfigurationer som stöds

Alternativet att begränsa en app till en speciell uppsättning användare eller säkerhets grupper i en klient organisation fungerar med följande typer av program:

- Program som kon figurer ATS för federerad enkel inloggning med SAML-baserad autentisering
- Application Proxy-program som använder Azure AD pre-Authentication
- Program som byggts direkt på den Azure AD-programplattform som använder OAuth 2.0/OpenID Connect-autentisering när en användare eller administratör har samtyckt till det programmet.

     > [!NOTE]
     > Den här funktionen är endast tillgänglig för webbapp/webb-API och företags program. Appar som är registrerade som [interna](./quickstart-register-app.md) kan inte begränsas till en uppsättning användare eller säkerhets grupper i klienten.

## <a name="update-the-app-to-enable-user-assignment"></a>Uppdatera appen för att aktivera användar tilldelning

Det finns två sätt att skapa ett program med aktive rad användar tilldelning. En kräver rollen **Global administratör** , den andra inte.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Företags program (kräver rollen som global administratör)

1. Gå till [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör**.
1. I det översta fältet väljer du det inloggade kontot. 
1. Under **katalog** väljer du den Azure AD-klient där appen ska registreras.
1. I navigeringen till vänster väljer du **Azure Active Directory**. Följ dessa steg om Azure Active Directory inte är tillgänglig i navigerings fönstret:

    1. Välj **alla tjänster** överst i den huvudsakliga navigerings menyn i den vänstra menyn.
    1. Skriv **Azure Active Directory** i rutan filtrera sökning och välj sedan **Azure Active Directory** objektet från resultatet.

1. I fönstret **Azure Active Directory** väljer du **företags program** från **Azure Active Directory** vänster navigerings meny.
1. Välj **Alla program** för att visa en lista över alla dina program.

     Om du inte ser det program som du vill visa här, använder du de olika filtren överst i listan **alla program** för att begränsa listan eller bläddra nedåt i listan för att hitta programmet.

1. Välj det program som du vill tilldela en användare eller säkerhets grupp till i listan.
1. På sidan **Översikt** för program väljer du **Egenskaper** från programmets vänstra navigerings meny.
1. Leta upp inställningen **användar tilldelning krävs?** och ange den till **Ja**. När det här alternativet är inställt på **Ja** måste användare i klient organisationen först tilldelas till det här programmet, annars kan de inte logga in i programmet.
1. Välj **Spara** för att spara den här konfigurations ändringen.

### <a name="app-registration"></a>Appregistrering

1. Gå till [**Azure Portal**](https://portal.azure.com/).
1. I det översta fältet väljer du det inloggade kontot. 
1. Under **katalog** väljer du den Azure AD-klient där appen ska registreras.
1. I navigeringen till vänster väljer du **Azure Active Directory**.
1. I fönstret **Azure Active Directory** väljer du **app-registreringar** från **Azure Active Directory** vänster navigerings meny.
1. Skapa eller Välj den app som du vill hantera. Du måste vara **ägare** till den här appens registrering.
1. På sidan **Översikt** för program följer du länken **hanterat program i lokal katalog** under Essentials överst på sidan. Detta tar dig till det _hanterade företags programmet_ för din app-registrering.
1. Från navigerings bladet till vänster väljer du **Egenskaper**.
1. Leta upp inställningen **användar tilldelning krävs?** och ange den till **Ja**. När det här alternativet är inställt på **Ja** måste användare i klient organisationen först tilldelas till det här programmet, annars kan de inte logga in i programmet.
1. Välj **Spara** för att spara den här konfigurations ändringen.

## <a name="assign-users-and-groups-to-the-app"></a>Tilldela användare och grupper till appen

När du har konfigurerat din app för att aktivera användar tilldelning kan du gå vidare och tilldela användare och grupper till appen.

1. Välj fönstret **användare och grupper** i den vänstra navigerings menyn i företags programmet.
1. Klicka på knappen **Lägg till användare** längst upp i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .
1. Välj **användarens** väljare i fönstret **Lägg till tilldelning** . 

     En lista över användare och säkerhets grupper visas tillsammans med en text ruta där du kan söka efter och hitta en viss användare eller grupp. På den här skärmen kan du välja flera användare och grupper i en enda go.

1. När du är färdig med att välja användare och grupper trycker du på knappen **Välj** längst ned för att gå vidare till nästa del.
1. Valfritt Om du har definierat app-roller i programmet kan du använda alternativet **Välj roll** för att tilldela de valda användarna och grupperna till en av programmets roller. 
1. Tryck på knappen **tilldela** längst ned för att slutföra tilldelningen av användare och grupper till appen. 
1. Bekräfta att de användare och grupper som du har lagt till visas i listan uppdaterade **användare och grupper** .

## <a name="more-information"></a>Mer information

- [Gör så här: Lägg till app-roller i ditt program](./howto-add-app-roles-in-azure-ad-apps.md)
- [Lägg till auktorisering med hjälp av app-roller & roller anspråk till en ASP.NET Core webbapp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Använda säkerhets grupper och program roller i dina appar (video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, nu med grupp anspråk och program roller](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory-appmanifest](./reference-app-manifest.md)
