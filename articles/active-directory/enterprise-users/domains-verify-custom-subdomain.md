---
title: Ändra autentiseringstyp för underdomän med PowerShell och Graph – Azure Active Directory | Microsoft Docs
description: Ändra standardinställningarna för underdomänautentisering som ärvts från rotdomäninställningarna i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4acf01a6672d17e62b6ebf5c6f43c8d6145f95a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739322"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Ändra autentiseringstyp för underdomän i Azure Active Directory

När en rotdomän har lagts till i Azure Active Directory (Azure AD) ärver alla efterföljande underdomäner som läggs till i roten i Din Azure AD-organisation automatiskt autentiseringsinställningen från rotdomänen. Men om du vill hantera domänautentiseringsinställningar oberoende av rotdomäninställningarna kan du nu använda Microsoft Graph-API:et. Om du till exempel har en federerad rotdomän, till exempel contoso.com, kan den här artikeln hjälpa dig att verifiera en underdomän, till exempel child.contoso.com som hanterad i stället för federerad.

När den överordnade domänen är federerad och administratören försöker verifiera en hanterad  underdomän på sidan Anpassade domännamn i Azure AD-portalen får du felet "Det gick inte att lägga till domän" med orsaken "En eller flera egenskaper innehåller ogiltiga värden". Om du försöker lägga till den här underdomänen från Administrationscenter för Microsoft 365 visas ett liknande fel. Mer information om felet finns i En underordnad domän ärver inte överordnade domänändringar i [Office 365, Azure eller Intune.](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes)

## <a name="how-to-verify-a-custom-subdomain"></a>Så här verifierar du en anpassad underdomän

Eftersom underdomäner ärver autentiseringstypen för rotdomänen som standard måste du höja upp underdomänen till en rotdomän i Azure AD med hjälp av Microsoft Graph så att du kan ange autentiseringstypen till önskad typ.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Lägg till underdomänen och visa dess autentiseringstyp

1. Använd PowerShell för att lägga till den nya underdomänen, som har rotdomänens standardautentiseringstyp. Azure AD och Microsoft 365 har ännu inte stöd för den här åtgärden.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Använd [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net) för att HÄMTA domänen. Eftersom domänen inte är en rotdomän ärver den autentiseringstypen för rotdomänen. Ditt kommando och resultat kan se ut så här med ditt eget klientorganisations-ID:

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

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Använda Azure AD Graph Explorer API för att göra detta till en rotdomän

Använd följande kommando för att höja upp underdomänen:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Ändra autentiseringstyp för underdomän

1. Använd följande kommando för att ändra autentiseringstypen för underdomänen:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Kontrollera via GET i Azure AD Graph Explorer att autentiseringstypen underdomän nu hanteras:

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
- [ForceDelete ett anpassat domännamn med Microsoft Graph API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)