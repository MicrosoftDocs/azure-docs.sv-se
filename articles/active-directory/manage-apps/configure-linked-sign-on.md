---
title: Förstå länkad inloggning i Azure Active Directory
description: Förstå länkad inloggning i Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 3e2d3ab6a23ce588c3aa393e5096ac75e88a5365
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255293"
---
# <a name="understand-linked-sign-on"></a>Förstå länkad inloggning

I [snabb starts serien](view-applications-portal.md) för program hantering har du lärt dig hur du använder Azure AD som identitets leverantör (IdP) för ett program. I snabb starts guiden konfigurerar du SAML-baserad eller OIDC-baserad SSO. Ett annat alternativ är **länkat**. Den här artikeln går till mer information om det länkade alternativet.

Med det **länkade** alternativet kan du konfigurera mål platsen när en användare väljer appen i din organisations [Mina appar](https://myapps.microsoft.com/) eller på Office 365-portalen.

Några vanliga scenarier där länk alternativet är värdefullt är:
- Lägg till en länk till ett anpassat webb program som för närvarande använder Federation, till exempel Active Directory Federation Services (AD FS) (AD FS).
- Lägg till djup länkar till vissa SharePoint-sidor eller andra webb sidor som du bara vill ska visas på användarens åtkomst paneler.
- Lägg till en länk till en app som inte kräver autentisering. 
 
 Det **länkade** alternativet ger inte inloggnings funktioner via autentiseringsuppgifter för Azure AD. Men du kan fortfarande använda några av de andra funktionerna i **företags program**. Du kan till exempel använda gransknings loggar och lägga till en anpassad logo typ och ett namn på appen.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill öka kunskapen snabbt kan du gå igenom [snabb starts serien](view-applications-portal.md) för program hantering. I snabb starten, där du konfigurerar enkel inloggning, hittar du också det **länkade** alternativet. 

Det **länkade** alternativet ger inte inloggnings funktioner via Azure AD. Alternativet anger bara de plats användare som ska skickas till när de väljer appen i [Mina appar](https://myapps.microsoft.com/) eller Microsoft 365 App Launcher.  Eftersom inloggningen inte tillhandahåller inloggnings funktioner via Azure AD är villkorlig åtkomst inte tillgänglig för program som kon figurer ATS med länkad enkel inloggning.

> [!IMPORTANT] 
> Det finns vissa scenarier där alternativet för **enkel inloggning** inte kommer att ingå i navigeringen för ett program i **företags program**. 
>
> Om programmet registrerades med hjälp av **Appregistreringar** , är funktionen för enkel inloggning inställd på att använda OIDC OAuth som standard. I det här fallet visas inte alternativet för **enkel inloggning** i navigeringen under **företags program**. När du använder **Appregistreringar** för att lägga till din anpassade app konfigurerar du alternativ i manifest filen. Mer information om manifest filen finns i [Azure Active Directory app manifest](../develop/reference-app-manifest.md). Mer information om SSO-standarder finns i [autentisering och auktorisering med Microsoft Identity Platform](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform). 
>
> Andra scenarier där **enkel inloggning** kommer att saknas i navigeringen är när ett program finns i en annan klient organisation eller om ditt konto inte har de behörigheter som krävs (global administratör, moln program administratör, program administratör eller ägare till tjänstens huvud namn). Behörigheter kan också orsaka ett scenario där du kan öppna **enkel inloggning** men inte kan spara. Mer information om administrativa roller i Azure AD finns i ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Konfigurera länk

Om du vill ange en länk till en app väljer du **länkad** på sidan **enkel inloggning** . Ange sedan länken och välj **Spara**. Behöver du en påminnelse om var du hittar de här alternativen? Kolla in [snabb starts serien](view-applications-portal.md).
 
När du har konfigurerat en app tilldelar du den till användare och grupper. När du tilldelar användare kan du styra när programmet ska visas i [Mina appar](https://myapps.microsoft.com/) eller i Microsoft 365 App Launcher.

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](./assign-user-or-group-access-portal.md)
- [Konfigurera automatisk etablering av användar konto](../app-provisioning/configure-automatic-user-provisioning-portal.md)
