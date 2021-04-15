---
title: 'Snabbstart: Ta bort ett program från din Azure Active Directory klient (Azure AD)'
description: Den här snabbstarten använder Azure Portal för att ta bort ett program från din Azure Active Directory (Azure AD) klientorganisation.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 1/5/2021
ms.author: iangithinji
ms.openlocfilehash: 55607d520affe704cecd2e16fb64b3a65c04e167
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374309"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Snabbstart: Ta bort ett program från din Azure Active Directory klient (Azure AD)

Den här snabbstarten använder Azure Portal för att ta bort ett program som har lagts till i din Azure Active Directory (Azure AD)-klientorganisation.

Läs mer om enkel inloggning och Azure i [Vad är enkel inloggning Sign-On (SSO).](what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill ta bort ett program från Din Azure AD-klientorganisation behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- En av följande roller: Global administratör, Molnprogramadministratör, Programadministratör eller ägare av tjänstens huvudnamn.
- Valfritt: Slutförande [av Visa dina appar](view-applications-portal.md).
- Valfritt: Slutförande [av Lägg till en app](add-application-portal.md).
- Valfritt: Slutförande [av Konfigurera en app](add-application-portal-configure.md).
- Valfritt: [Slutförande av Tilldela användare till en app](add-application-portal-assign-users.md).
- Valfritt: [Slutförande av konfigurera enkel inloggning](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Använd en icke-produktionsmiljö för att testa stegen i den här snabbstarten.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Ta bort ett program från din Azure AD-klientorganisation

Så här tar du bort ett program från Azure AD-klientorganisationen:

1. I Azure AD-portalen väljer du **Företagsprogram.** Leta sedan upp och välj det program som du vill ta bort. I det här fallet tog vi bort **GitHub_test** som vi lade till i föregående snabbstart.
1. I avsnittet **Hantera** i den vänstra rutan väljer du **Egenskaper.**
1. Välj **Ta** bort och välj sedan **Ja för** att bekräfta att du vill ta bort appen från din Azure AD-klientorganisation.

> [!TIP]
> Du kan automatisera apphantering med hjälp av Graph API i [Automatisera apphantering med Microsoft Graph API.](/graph/application-saml-sso-configure-api)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabbstartsserien bör du överväga att ta bort appen för att rensa testklienten. I den här snabbstarten gick vi in på att ta bort appen.

## <a name="next-steps"></a>Nästa steg

Du har slutfört snabbstartsserien! Mer information om enkel inloggning Sign-On (SSO) finns [i Vad är enkel inloggning?](what-is-single-sign-on.md) Eller läs om metodtips för apphantering.
> [!div class="nextstepaction"]
> [Metodtips för programhantering](application-management-fundamentals.md)
