---
title: Förstå länkad inloggning i Azure Active Directory
description: Förstå länkad inloggning i Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: iangithinji
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 6ed6f6b69326157573ea043457dbc8d8a3079146
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374582"
---
# <a name="understand-linked-sign-on"></a>Förstå länkad inloggning

I [snabbstartsserien](view-applications-portal.md) om programhantering lärde du dig att använda Azure AD som identitetsprovider (IdP) för ett program. I snabbstartsguiden konfigurerar du SAML-baserad eller OIDC-baserad enkel inloggning. Ett annat alternativ är **Länkad**. Den här artikeln innehåller mer information om det länkade alternativet.

Med **alternativet** Länkad kan du konfigurera målplatsen när en användare väljer appen i organisationens [Mina appar](https://myapps.microsoft.com/) eller Office 365-portalen.

Några vanliga scenarier där länkalternativet är värdefullt är:
- Lägg till en länk till en anpassad webbapp som för närvarande använder federation, till exempel Active Directory Federation Services (AD FS) (AD FS).
- Lägg till djuplänkar till specifika SharePoint-sidor eller andra webbsidor som du bara vill ska visas på användarens åtkomstpaneler.
- Lägg till en länk till en app som inte kräver autentisering. 
 
 Alternativet **Länkad** tillhandahåller inte inloggningsfunktioner via Azure AD-autentiseringsuppgifter. Men du kan fortfarande använda några av de andra funktionerna i **Företagsprogram**. Du kan till exempel använda granskningsloggar och lägga till en anpassad logotyp och ett appnamn.

## <a name="before-you-begin"></a>Innan du börjar

Om du snabbt vill få kunskap kan du gå igenom [snabbstartsserien](view-applications-portal.md) om programhantering. I snabbstarten, där du konfigurerar enkel inloggning, hittar du även alternativet **Länkad.** 

Alternativet **Länkad** tillhandahåller inte inloggningsfunktioner via Azure AD. Alternativet anger bara den plats som användarna ska skickas till när de väljer appen [på Mina appar](https://myapps.microsoft.com/) eller Microsoft 365-appstartaren.  Eftersom inloggningen inte tillhandahåller inloggningsfunktioner via Azure AD är villkorlig åtkomst inte tillgänglig för program som konfigurerats med länkad enkel inloggning.

> [!IMPORTANT] 
> Det finns vissa scenarier där **alternativet enkel inloggning inte** finns i navigeringen för ett program i **Företagsprogram.** 
>
> Om programmet registrerades med **Appregistreringar** är funktionen för enkel inloggning konfigurerad att använda OIDC OAuth som standard. I det här fallet **visas inte alternativet Enkel** inloggning i navigeringen under **Företagsprogram**. När du använder **Appregistreringar för** att lägga till din anpassade app konfigurerar du alternativ i manifestfilen. Mer information om manifestfilen finns i Azure Active Directory [appmanifest](../develop/reference-app-manifest.md). Mer information om standarder för enkel inloggning finns i Autentisering [och auktorisering med Microsoft Identity Platform.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Andra scenarier **där** enkel inloggning saknas i navigeringen är när ett program finns i en annan klientorganisation eller om ditt konto inte har de behörigheter som krävs (global administratör, molnprogramadministratör, programadministratör eller ägare av tjänstens huvudnamn). Behörigheter kan också orsaka ett scenario där **du kan öppna enkel** inloggning men inte kan spara. Mer information om administrativa roller i Azure AD finns i ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Konfigurera länk

Om du vill ange en länk för en app **väljer** du **Länkad på sidan Enkel** inloggning. Ange sedan länken och välj **Spara.** Behöver du en påminnelse om var du hittar dessa alternativ? Kolla in [snabbstartsserien](view-applications-portal.md).
 
När du har konfigurerat en app tilldelar du användare och grupper till den. När du tilldelar användare kan du styra när programmet visas [Mina appar](https://myapps.microsoft.com/) eller Microsoft 365 appstartaren.

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](./assign-user-or-group-access-portal.md)
- [Konfigurera automatisk användarkontoetablering](../app-provisioning/configure-automatic-user-provisioning-portal.md)
