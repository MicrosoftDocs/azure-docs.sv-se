---
title: Ändra autentiseringstyp för under domän med PowerShell och Graph-Azure Active Directory | Microsoft Docs
description: Ändra standard inställningarna för autentisering av under domäner ärvs från rot domän inställningar i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 734e6824f13e62ad080500eff18c4892e1f76807
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95503665"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Ändra autentiseringstyp för under domän i Azure Active Directory

När en rot domän har lagts till i Azure Active Directory (Azure AD) ärver alla efterföljande under domäner i den roten i din Azure AD-organisation automatiskt autentiseringsinställningarna från rot domänen. Men om du vill hantera inställningar för domänautentisering oberoende av rot domän inställningarna kan du nu med Microsoft Graph-API: et. Om du till exempel har en federerad rot domän, till exempel contoso.com, kan den här artikeln hjälpa dig att verifiera en under domän som child.contoso.com som hanterad i stället för federerad.

När den överordnade domänen är federerad i Azure AD-portalen och administratören försöker att verifiera en hanterad under domän på sidan **anpassade domän namn** , får du fel meddelandet "Det gick inte att lägga till domänen" med orsaken "en eller flera egenskaper innehåller ogiltiga värden". Om du försöker lägga till den här under domänen från Microsoft 365 administrations Center visas ett liknande fel. Mer information om felet finns i [en underordnad domän ärver inte överordnade domän ändringar i Office 365, Azure eller Intune](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes).

## <a name="how-to-verify-a-custom-subdomain"></a>Så här verifierar du en anpassad under domän

Eftersom under domäner ärver autentiseringstypen för rot domänen som standard, måste du befordra under domänen till en rot domän i Azure AD med hjälp av Microsoft Graph så att du kan ange autentiseringstypen till önskad typ.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Lägg till under domänen och visa dess autentiseringstyp

1. Använd PowerShell för att lägga till den nya under domänen, som har dess rotdomän som standard typ av autentisering. Azure AD-och Microsoft 365 administrations Center stöder ännu inte den här åtgärden.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Använd [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net) för att hämta domänen. Eftersom domänen inte är en rot domän ärver den autentiseringstypen för rot domänen. Ditt kommando och resultat kan se ut så här med ditt eget klient-ID:

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Använd Azure AD Graph Explorer API för att göra detta till en rotdomän

Använd följande kommando för att uppgradera under domänen:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Ändra typen av under domänens autentisering

1. Använd följande kommando för att ändra autentiseringstypen för under domänen:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Verifiera via Hämta i Azure AD Graph Explorer att under domänens autentiseringstyp hanteras nu:

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>Nästa steg

- [Lägga till anpassade domännamn](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Hantera domännamn](domains-manage.md)
- [ForceDelete ett anpassat domän namn med Microsoft Graph-API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)