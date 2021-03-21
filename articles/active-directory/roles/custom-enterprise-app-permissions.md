---
title: App-behörigheter för anpassade roller i Azure Active Directory | Microsoft Docs
description: För hands versionen av företags program behörigheter för anpassade Azure AD-roller i Azure Portal, PowerShell eller Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: d2159540c688a63082efb792fd4d261062ef65d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466801"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Behörigheter för företags program för anpassade roller i Azure Active Directory

Den här artikeln innehåller de behörigheter som för närvarande är tillgängliga för företags program för anpassade roll definitioner i Azure Active Directory (Azure AD). I den här artikeln hittar du behörighets listor för några vanliga scenarier och den fullständiga listan över behörigheter för företags program. Application Proxy-behörigheter är för närvarande inte distribuerade i den här versionen.

## <a name="required-license-plan"></a>Obligatorisk licens plan

Om du använder den här funktionen krävs en Azure AD Premium P1-licens för din Azure AD-organisation. Hitta rätt licens för dina behov i [Jämför allmänt tillgängliga funktioner i Free, Basic och Premium-utgåvorna](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="enterprise-application-permissions"></a>Behörigheter för företags program

Mer information om hur du använder dessa behörigheter finns i [tilldela anpassade roller för att hantera företags program](custom-enterprise-apps.md)

### <a name="assigning-users-or-groups-to-an-application"></a>Tilldela användare eller grupper till ett program

För att delegera tilldelningen av användare och grupper som har åtkomst till SAML-baserade enkla inloggnings program. Behörigheter som krävs

- Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update

### <a name="creating-gallery-applications"></a>Skapa Galleri program

För att delegera skapandet av Azure AD Gallery-program som ServiceNow, F5, Salesforce, bland andra. Behörigheter som krävs:

- Microsoft. Directory/applicationTemplates/instansiera

### <a name="configuring-basic-saml-urls"></a>Konfigurera grundläggande SAML-URL: er

För att delegera uppdateringen och läsningen av grundläggande SAML-konfigurationer för SAML-baserade enkla inloggnings program. Behörigheter som krävs:

- Microsoft. Directory/Service princip ALS/Authentication/Update
- Microsoft. Directory/Applications. min organisation/autentisering/uppdatering

### <a name="rolling-over-or-creating-signing-certs"></a>Löpande eller skapa signerings certifikat

För att delegera hanteringen av signerings certifikat för SAML-baserade enkla inloggnings program. Behörigheter som krävs.

Microsoft. Directory/program/autentiseringsuppgifter/uppdatera

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>Uppdatera förfallo datum för inloggnings certifikat meddelandets e-postadress

För att delegera uppdateringen av utgångna inloggnings certifikat e-postadresser för SAML-baserade enkla inloggnings program. Behörigheter som krävs:

- Microsoft. Directory/Applications. min organisation/autentisering/uppdatering
- Microsoft. Directory/Applications. min organisation/behörigheter/uppdatera
- Microsoft. Directory/Service princip ALS/Authentication/Update
- Microsoft. Directory/Service princip ALS/Basic/Update

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>Hantera signatur för SAML-token och inloggnings algoritm

Att delegera uppdateringen av signaturen för SAML-token och inloggnings algoritmen för SAML-baserade enkla inloggnings program. Behörigheter som krävs:

- Microsoft. Directory/applicationPolicies/Basic/Update
- Microsoft. Directory/program/autentisering/uppdatering
- Microsoft. Directory/Service princip ALS/policies/Update

### <a name="manage-user-attributes-and-claims"></a>Hantera användarattribut och anspråk

För att delegera skapande, borttagning och uppdatering av användarattribut och anspråk för SAML-baserade enkla inloggnings program. Behörigheter som krävs:

- Microsoft. Directory/applicationPolicies/Basic/Update
- Microsoft. Directory/program/autentisering/uppdatering
- Microsoft. Directory/Service princip ALS/policies/Update

## <a name="app-provisioning-permissions"></a>Konfigurations behörigheter för app

Att utföra Skriv åtgärder som att hantera jobb, schema eller autentiseringsuppgifter via användar gränssnittet kräver också Läs behörighet för att Visa etablerings sidan.

Att ange omfång för alla användare och grupper eller tilldelade användare och grupper kräver för närvarande både synchronizationJob-och synchronizationCredentials-behörighet.

### <a name="turn-on-or-restart-provisioning-jobs"></a>Aktivera eller starta om etablerings jobb

För att delegera möjlighet att aktivera, stänga av och starta om etablerings jobb. Behörigheter som krävs:

- Microsoft. Directory/Service princip ALS/synchronizationJobs/Manage  

### <a name="configure-the-provisioning-schema"></a>Konfigurera etablerings schema  

För att delegera uppdateringar till mappning av attribut. Behörigheter som krävs:

- Microsoft. Directory/Service princip ALS/synchronizationSchema/Manage  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>Läs etablerings inställningar som är associerade med programobjektet

För att delegera möjlighet att läsa etablerings inställningar som är associerade med objektet. Behörigheter som krävs:

- Microsoft. Directory/program/synkronisering/standard/Read

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>Läs etablerings inställningar som är associerade med tjänstens huvud namn

För att delegera möjlighet att läsa etablerings inställningar som är associerade med tjänstens huvud namn. Behörigheter som krävs:

- Microsoft. Directory/Service princip ALS/Synchronization/standard/Read

### <a name="authorize-application-access-for-provisioning"></a>Auktorisera program åtkomst för etablering  

För att delegera möjlighet att auktorisera program åtkomst för etablering. Exempel på indataport av OAuth Bearer-token. Behörigheter som krävs:

- Microsoft. Directory/Service princip ALS/synchronizationCredentials/Manage

## <a name="full-list-of-permissions"></a>Fullständig lista över behörigheter

Behörighet | Beskrivning
---------- | -----------
Microsoft. Directory/applicationPolicies/allProperties/Read | Läs alla egenskaper för användnings principer.
Microsoft. Directory/applicationPolicies/allProperties/Update | Uppdatera alla egenskaper för användnings principer.
Microsoft. Directory/applicationPolicies/Basic/Update | Uppdatera standard egenskaper för användnings principer.
Microsoft. Directory/applicationPolicies/Create | Skapa användnings principer.
Microsoft. Directory/applicationPolicies/createAsOwner | Skapa användnings principer. Skaparen har lagts till som första ägare.
Microsoft. Directory/applicationPolicies/Delete | Ta bort användnings principer.
Microsoft. Directory/applicationPolicies/Owners/Read | Läs ägare för användnings principer.
Microsoft. Directory/applicationPolicies/Owners/Update | Uppdatera egenskapen owner för användnings principer.
Microsoft. Directory/applicationPolicies/policyAppliedTo/Read | Läs program principer tillämpas på objekt listan.
Microsoft. Directory/applicationPolicies/standard/Read | Läsa standard egenskaper för användnings principer.
Microsoft. Directory/Service princip ALS/allProperties/allTasks | Skapa och ta bort service princip ALS och läsa och uppdatera alla egenskaper i Azure Active Directory.
Microsoft. Directory/Service princip ALS/allProperties/Read | Läsa alla egenskaper för service princip ALS.
Microsoft. Directory/Service princip ALS/allProperties/Update | Uppdatera alla egenskaper för service princip ALS.
Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Read | Läs roll tilldelningar för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update | Uppdatera roll tilldelningar för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/appRoleAssignments/Read | Läs roll tilldelningar tilldelade till tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/målgrupp/uppdatera | Uppdatera mål egenskaper för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/Authentication/Update | Uppdatera autentiserings egenskaper för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/Basic/Update | Uppdatera grundläggande egenskaper för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/Create | Skapa tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/createAsOwner | Skapa tjänstens huvud namn. Skaparen har lagts till som första ägare.
Microsoft. Directory/Service princip ALS/credentials/Update | Uppdatera egenskaper för autentiseringsuppgifter för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/Delete | Ta bort tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/Disable | Inaktivera tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/Enable | Aktivera tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/getPasswordSingleSignOnCredentials | Läs inloggnings uppgifter för lösen ord för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/managePasswordSingleSignOnCredentials | Hantera autentiseringsuppgifter för enkel inloggning med lösen ord för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/oAuth2PermissionGrants/Read | Läs delegerad behörighet beviljar för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/Owners/Read | Läs ägare till tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/Owners/Update | Uppdatera ägare för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/Permissions/Update |  
Microsoft. Directory/Service princip ALS/policies/Read | Läs principer för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/policies/Update | Uppdatera principer för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/standard/Read | Läs standard egenskaper för tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/Synchronization/standard/Read | Läs etablerings inställningar som är associerade med tjänstens huvud namn.
Microsoft. Directory/Service princip ALS/tag/Update | Uppdatera Tags-egenskapen för tjänstens huvud namn.
Microsoft. Directory/applicationTemplates/instansiera | Instansiera Galleri program från programmallar.
Microsoft. Directory/auditLogs/allProperties/Read | Läs gransknings loggar.
Microsoft. Directory/signInReports/allProperties/Read | Läs inloggnings rapporter.
Microsoft. Directory/program/synkronisering/standard/Read | Läs etablerings inställningar som är associerade med programobjektet.
Microsoft. Directory/Service princip ALS/synchronizationJobs/Manage | Hantera alla aspekter av jobb synkronisering för tjänst huvud resurser
Microsoft. Directory/Service princip ALS/Synchronization/standard/Read | Läs etablerings inställningar som är associerade med tjänstens huvud namn
Microsoft. Directory/Service princip ALS/synchronizationSchema/Manage | Hantera alla aspekter av synkroniseringsschemat för resurser för tjänstens huvud namn
Microsoft. Directory/provisioningLogs/allProperties/Read | Läs alla egenskaper för etablerings loggar

## <a name="next-steps"></a>Nästa steg

- [Skapa anpassade roller med hjälp av Azure Portal, Azure AD PowerShell och Graph API](custom-create.md)
- [Visa lista över rolltilldelningar](view-assignments.md)
